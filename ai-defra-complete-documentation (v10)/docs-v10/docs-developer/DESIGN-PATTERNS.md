# Design Patterns

[← Back to Developer Docs](README.md)

---

## 1. Retrieval-Augmented Generation (RAG)

The core AI pattern in this system. Rather than relying solely on the LLM's training knowledge, RAG grounds responses in documents the user has uploaded.

**How it works here:**

1. User sends a prompt with one or more `knowledge_group_ids`
2. Agent embeds the prompt via Bedrock Titan Embed v2 (1024-dim vector)
3. Agent calls Knowledge service `POST /rag/search { query, knowledge_group_ids }`
4. Knowledge service runs a nearest-neighbour similarity search in PostgreSQL (pgvector) and returns the top matching chunks
5. Agent prepends the matched chunks to the prompt and calls Bedrock `converse()`
6. If no `knowledge_group_ids` are provided, steps 2–4 are skipped and the LLM answers from general knowledge only

**Current implementation:** intentionally minimal — nearest-neighbour lookup, no re-ranking, no query expansion, no metadata filtering. This is by design as a baseline reference.

**Chunking:** 800 characters per chunk, 100 character overlap. JSONL files use each line as a pre-formed chunk.

---

## 2. Async Queue-and-Poll

LLM inference takes several seconds — too long for a synchronous HTTP response. The system handles this with a queue-and-poll pattern.

**Flow:**

```
POST /chat
  → Agent saves message (status: queued)
  → Enqueues { message_id, conversation_id, question } to AWS SQS
  → Returns 202 immediately with { conversation_id, message_id }

SQS Worker (background)
  → Polls SQS continuously
  → Dequeues message
  → Performs RAG lookup if needed
  → Calls Bedrock converse()
  → Saves completed response to MongoDB
  → Deletes SQS message

Frontend
  → Redirects to conversation view
  → Polls GET /conversations/{conversationId}
  → Updates UI when status changes to completed
```

**Why SQS:** decouples the frontend from LLM latency; enables retry on failure; allows the worker to scale independently.

---

## 3. CDP Uploader Callback Pattern

File uploads do not go directly to the Knowledge service. They use DEFRA's CDP Uploader service, which handles the S3 upload and fires a callback when complete.

**Flow:**

1. Frontend calls CDP Uploader `POST /initiate { redirect, callback, s3Bucket, s3Path, metadata }` → receives an `uploadReference` UUID
2. User uploads file directly to CDP Uploader
3. CDP Uploader stores file in S3, then fires callback to Frontend
4. Frontend notifies Knowledge service of the completed upload (with S3 key and metadata)
5. Knowledge service triggers ingestion as a background task

**Why this pattern:** CDP Uploader handles large file transfers, virus scanning, and S3 storage consistently across all CDP services.

---

## 4. Background Ingestion with asyncio

Document ingestion (extract → chunk → embed → store) is potentially slow and runs as a fire-and-forget background task per document.

```python
asyncio.create_task(ingest_document(document_id, s3_key, knowledge_group_id))
```

Status is tracked in MongoDB: `not_started → in_progress → ready / failed`.

The frontend can poll document status to show ingest progress to the user.

---

## 5. Conversation History Management

The Agent persists every conversation turn to MongoDB. When a new prompt arrives:

1. Load full conversation history from MongoDB
2. Perform RAG lookup (if required)
3. Send `converse(history + new prompt, system_prompt, model)` to Bedrock
4. Save the assistant's response to the conversation record

Redis (Frontend) holds a short-term cache of conversation state for resilience during API transitions, but MongoDB is the source of truth.

---

## 6. Traefik Reverse Proxy

All services in the local dev stack run behind a Traefik reverse proxy. The frontend is accessed at `http://frontend.localhost` — not a direct port.

This mirrors the production CDP routing pattern, ensuring local dev behaviour matches deployment as closely as possible.

---

## 7. WireMock Stub for AWS Bedrock

The Bedrock Stub (WireMock) intercepts calls to both the Claude Converse API and the Titan Embed API. It uses priority-based request matching and configures a 2-second simulated delay to approximate real LLM latency.

This pattern allows the entire stack to run offline and makes the test suite deterministic — responses are fixed JSON payloads rather than live LLM outputs.
