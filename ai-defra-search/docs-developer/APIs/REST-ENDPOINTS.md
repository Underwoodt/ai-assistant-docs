# REST Endpoints

[Back to Developer Docs](../README.md)

---

> **Warning: Assumed** — endpoint signatures below are inferred from the architecture document (ADR-004) sequence diagrams. Verify exact parameter names and response schemas against the FastAPI auto-generated docs at `/docs` on each service.

---

## Frontend Service (Node.js / Hapi)

The Frontend Service exposes routes consumed by the browser and by CDP Uploader callbacks.

| Method | Path | Description |
|---|---|---|
| `GET` | `/start` | Chat screen — loads knowledge groups and available models |
| `GET` | `/start/{conversationId}` | Conversation page — polls for completed response |
| `POST` | `/start` | Submit a new prompt; enqueues via Agent Service, redirects to conversation page |
| `GET` | `/upload` | Upload screen — lists user's knowledge groups |
| `POST` | `/upload/create-group` | Create a new knowledge group |
| `POST` | `/upload` | Initiate a file upload via CDP Uploader |
| `GET` | `/upload/files/{uploadReference}` | File selection page after upload initiated |
| `GET` | `/upload-status/{uploadReference}` | Poll CDP Uploader status URL |
| `POST` | `/upload/callback/{uploadReference}` | CDP Uploader callback — triggers document ingestion |

---

## Agent Service (Python / FastAPI)

The Agent Service is called by the Frontend and exposes conversation management endpoints.

| Method | Path | Body / Params | Response | Description |
|---|---|---|---|---|
| `POST` | `/chat` | `{ question, conversation_id, model_id, knowledge_group_ids }` + `user-id` header | `202 { conversation_id, message_id, status: queued }` | Enqueue a new chat message |
| `GET` | `/conversations/{conversationId}` | — | Conversation with messages + status | Retrieve full conversation (used by frontend polling) |

**Auto-generated API docs:** `GET /docs` (FastAPI Swagger UI) when running locally.

---

## Knowledge Service (Python / FastAPI)

The Knowledge Service is called by the Frontend (for uploads) and by the Agent Service (for RAG search).

| Method | Path | Body / Params | Response | Description |
|---|---|---|---|---|
| `GET` | `/knowledge-groups` | `user-id` header | List of knowledge groups | List knowledge groups for the authenticated user |
| `POST` | `/knowledge-group` | `{ name, description }` + `user-id` header | Knowledge group object | Create a new knowledge group |
| `POST` | `/documents` | `{ file_name, s3_key, knowledge_group_id, cdp_upload_id }` + `user-id` header | `201 Accepted` | Trigger document ingest (async background task) |
| `POST` | `/rag/search` | `{ query, knowledge_group_ids }` | `{ chunks: [{ content, file_name, s3_key, score }] }` | Semantic search — returns matched chunks with scores |

**Auto-generated API docs:** `GET /docs` (FastAPI Swagger UI) when running locally.

---

## CDP Uploader (External Platform Service)

The CDP Uploader is a platform-managed service. The Frontend initiates uploads by calling it.

| Method | Path | Description |
|---|---|---|
| `POST` | `/initiate` | Initiate an upload session; returns `uploadId` and `statusUrl` |
| `POST` | `/upload-and-scan/{uploadId}` | Client submits files directly to CDP Uploader for scanning and S3 delivery |
| `GET` | `{statusUrl}` | Poll upload status (`pending` / `complete` / `error`) |
