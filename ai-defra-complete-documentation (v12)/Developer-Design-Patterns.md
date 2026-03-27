# Design Patterns

> 👩‍💻 **Developer Docs** — [Home](Home) › [Developer Docs](Home#developer-docs)

---

## 1. Retrieval-Augmented Generation (RAG)

The core AI pattern. Rather than relying solely on the LLM's training knowledge, RAG grounds responses in documents the user has uploaded.

**Flow:**
1. User sends prompt with `knowledge_group_ids`
2. Agent embeds prompt via Bedrock Titan Embed v2 (1024-dim)
3. Agent calls Knowledge `POST /rag/search { query, knowledge_group_ids }`
4. Knowledge runs nearest-neighbour similarity search in PostgreSQL (pgvector) → returns top chunks
5. Agent prepends chunks to the prompt and calls Bedrock `converse()`
6. No `knowledge_group_ids`? Steps 2–4 are skipped; LLM answers from general knowledge only

**Current implementation:** intentionally minimal — nearest-neighbour only, no re-ranking, no query expansion, no metadata filtering.

**Chunking:** 800 characters, 100 character overlap. JSONL files: each line = one pre-formed chunk.

---

## 2. Async Queue-and-Poll

LLM inference takes several seconds — too long for a synchronous HTTP response.

```
POST /chat
  → Agent saves message (status: queued)
  → Enqueues { message_id, conversation_id, question } to AWS SQS
  → Returns 202 immediately

SQS Worker (background)
  → Polls SQS continuously
  → Dequeues, performs RAG if needed
  → Calls Bedrock converse()
  → Saves response to MongoDB
  → Deletes SQS message

Frontend
  → Polls GET /conversations/{id}
  → Updates UI on completion
```

**Why SQS:** decouples frontend from LLM latency; enables retry on failure; worker scales independently.

---

## 3. CDP Uploader Callback Pattern

File uploads use DEFRA's CDP Uploader service rather than going directly to the Knowledge service.

**Flow:**
1. Frontend calls CDP Uploader `POST /initiate` → receives `uploadReference` UUID
2. User uploads file to CDP Uploader → stored in S3
3. CDP Uploader fires callback to Frontend with S3 key
4. Frontend notifies Knowledge service `POST /knowledge-groups/{id}/documents`
5. Knowledge triggers ingestion as a background task

**Why:** CDP Uploader handles large file transfers, virus scanning, and S3 storage consistently across all CDP services.

---

## 4. Background Ingestion with asyncio

Document ingestion is slow and runs fire-and-forget per document:

```python
asyncio.create_task(ingest_document(document_id, s3_key, knowledge_group_id))
```

Status tracked in MongoDB: `not_started → in_progress → ready / failed`.

---

## 5. Conversation History Management

Every turn is persisted to MongoDB. On a new prompt:
1. Load full history from MongoDB
2. Perform RAG lookup if `knowledge_group_ids` provided
3. Send `converse(history + new prompt, system_prompt, model)` to Bedrock
4. Save assistant response

Redis (Frontend) holds a short-term cache for resilience — MongoDB is the source of truth.

---

## 6. Traefik Reverse Proxy

All local services run behind Traefik. Frontend accessed at `http://frontend.localhost` — mirrors CDP production routing.

---

## 7. WireMock Stub for AWS Bedrock

Intercepts both Claude Converse and Titan Embed API calls. Uses priority-based request matching with a **2-second simulated delay** for realistic latency. Allows the entire stack to run offline with deterministic responses.
