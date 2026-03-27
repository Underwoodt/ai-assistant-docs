# Design Patterns

[Back to Developer Docs](./README.md)

---

## 1. RAG Pipeline (Retrieval-Augmented Generation)

The core architectural pattern of the system. Rather than relying purely on an LLM's training data, the Agent Service optionally retrieves relevant document chunks from the Knowledge Service before calling Bedrock.

**Pattern:** Query → Embed → Vector Search → Augmented Prompt → LLM

| Step | Service | Detail |
|---|---|---|
| Query received | Agent Service | User prompt arrives via `POST /chat` |
| RAG decision | Agent Service | If `knowledge_group_ids` present, RAG is required |
| Embed query | Knowledge Service → Bedrock | Titan Embed v2, produces 1024-dim vector |
| Vector similarity search | Knowledge Service → PostgreSQL | pgvector nearest-neighbour lookup |
| Augment prompt | Agent Service | Prepend matched chunks to the prompt sent to Bedrock |
| LLM inference | Agent Service → Bedrock | Claude model called with augmented context |

**Intentional simplicity:** This is a nearest-neighbour baseline. No re-ranking, no hybrid keyword search, no query expansion. The clean service boundary between Agent and Knowledge makes it straightforward to extend.

---

## 2. Async Queue Pattern (Fire-and-Poll)

LLM inference latency makes synchronous HTTP request-response impractical for user-facing chat. The system uses an async queue pattern:

1. Frontend POSTs a prompt → Agent Service enqueues to **AWS SQS**, returns `202` immediately with a `conversation_id`
2. Frontend redirects to a conversation page and polls `GET /start/{conversationId}`
3. Agent Service worker polls SQS, processes the message (calling Bedrock), saves result to MongoDB
4. Next frontend poll finds status `completed` and displays the response

**Why SQS over WebSockets/SSE:** Aligns with CDP infrastructure, supports worker scaling independently of the API tier, provides at-least-once delivery with visibility timeout.

---

## 3. Document Chunking + Embedding Pipeline

The Knowledge Service implements a structured ingest pipeline per document type:

| File Type | Extractor |
|---|---|
| PDF | `PdfChunkExtractor` |
| DOCX | `DocxChunkExtractor` |
| PPTX | `PptxChunkExtractor` |
| JSONL | `JsonlChunkExtractor` (each line = pre-formed chunk) |

**Chunking strategy:** ~800 characters per chunk, 100-character overlap. This balances retrieval granularity against LLM context window size.

**Async background task:** The `POST /documents` endpoint returns `201 Accepted` immediately; actual ingest runs as a background task to avoid blocking the caller.

---

## 4. CDP Uploader Integration Pattern

File uploads are not handled directly by the application — they are delegated to the **CDP Uploader**, a platform service that handles virus scanning and S3 delivery. The pattern is:

1. Frontend initiates with CDP Uploader (`POST /initiate`) → receives `uploadReference` and `statusUrl`
2. Client browser uploads files directly to CDP Uploader
3. CDP Uploader scans, stores in S3, fires callback to Frontend (`POST /upload/callback/{uploadReference}`)
4. Frontend filters complete vs rejected files, notifies Knowledge Service to ingest

**Why:** Security (virus scanning), compliance, and separation of concerns. The application never handles raw file bytes directly in the upload path.

---

## 5. User Identity Propagation via Header

Internal services do not authenticate requests independently. The Frontend acts as the authentication boundary (Entra ID) and forwards the authenticated user identity as a `user-id` HTTP header to all downstream services.

**Implication:** Backend services (Agent, Knowledge) must not be exposed directly to the internet — they trust the header unconditionally.

---

## 6. Offline Development with WireMock Stub

The `ai-defra-search-aws-bedrock-stub` repo provides a **WireMock** service that stubs both the Claude Converse API and the Titan Embed Text V2 API. This enables:

- Full local development with no AWS credentials
- Deterministic test responses (priority-based matching, JSONPath patterns)
- Simulated latency (2-second delay on all responses)

This pattern is essential for CI/CD pipeline execution and onboarding new developers quickly.

---

## 7. Conversation State Machine

Each message in a conversation passes through a defined status lifecycle managed by the Agent Service:

```
queued → processing → completed
                   → failed
```

The Frontend uses this status to display the appropriate UI state (loading spinner, response, or error). Redis caches a placeholder conversation object between the initial enqueue and the first successful poll.
