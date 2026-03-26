# REST API Reference

> **Source:** `ai-defra-search-agent` and `ai-defra-search-knowledge` source analysis

All endpoints require `X-API-KEY` header except `/health` and `/docs`.

---

## Agent Service — `http://localhost:8086`

### POST /chat

Queues a user question for asynchronous processing.

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `question` | string | ✓ | The user's question |
| `model_id` | string | ✓ | LLM model identifier (e.g. `claude-3-5-sonnet`) |
| `conversation_id` | UUID | | Omit to start a new conversation |
| `knowledge_group_ids` | list[string] | | IDs of knowledge groups to search; empty = LLM only |

**Response** `202 Accepted`

| Field | Type | Description |
|---|---|---|
| `message_id` | UUID | ID of the queued message |
| `conversation_id` | UUID | Conversation this message belongs to |
| `status` | string | `"queued"` |

**Errors:** `400` Bad request · `404` Conversation not found · `503` Service unavailable

---

### GET /conversations/{conversation_id}

Retrieves a full conversation including all messages.

**Path parameters**

| Parameter | Type | Description |
|---|---|---|
| `conversation_id` | UUID | Conversation to retrieve |

**Response** `200 OK`

| Field | Type | Description |
|---|---|---|
| `conversation_id` | UUID | |
| `messages` | list[Message] | Array of all messages in order |

**Message object**

| Field | Type | Description |
|---|---|---|
| `message_id` | UUID | |
| `role` | string | `"user"` or `"assistant"` |
| `content` | string | Message text |
| `model_name` | string | Human-readable model name |
| `model_id` | string | Model identifier |
| `status` | string | `"complete"`, `"pending"`, `"error"` |
| `error_message` | string | Set if status is `"error"` |
| `rag_error` | string | Set if knowledge retrieval failed |
| `timestamp` | datetime | ISO 8601 |

**Errors:** `404` Not found · `503` Service unavailable

---

### GET /models

Lists available LLM models. Requires `X-API-KEY`.

**Response** `200 OK` — Array of model objects with `model_id` and `model_name`.

---

### POST /feedback

Submit user feedback on a response. Requires `X-API-KEY`.

> ⚠ Full schema not captured — verify in `ai-defra-search-agent/app/feedback/`

---

### GET /health

Health check. No authentication required.

**Response** `200 OK`

---

## Knowledge Service — `http://localhost:8085`

### POST /documents (upload)

Upload a document to a knowledge group.

> ⚠ Exact schema — verify in `ai-defra-search-knowledge/app/document/router.py`

**Supported formats:** PDF, DOCX, PPTX, JSONL

---

### POST /rag/search (internal)

Called by the Agent service to retrieve relevant document chunks for a query.

**Request body**

| Field | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✓ | The user's question (will be vectorised) |
| `knowledge_group_ids` | list[string] | ✓ | Groups to search |

**Response** — Ranked list of document chunks with similarity scores.

> ⚠ Full response schema — verify in `ai-defra-search-knowledge/app/rag/router.py`

---

### Knowledge Group endpoints

Manage personal knowledge groups (create, list, delete).

> ⚠ Full CRUD schema — verify in `ai-defra-search-knowledge/app/knowledge_group/`

---

### GET /health

Health check. No authentication required.

---

## Swagger / OpenAPI

Both services expose interactive API docs at their `/docs` endpoint:
- Agent: http://localhost:8086/docs
- Knowledge: http://localhost:8085/docs
