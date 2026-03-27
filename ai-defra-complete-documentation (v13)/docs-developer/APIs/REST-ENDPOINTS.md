# REST Endpoints

[← Back to Developer Docs](../README.md)



> Interactive docs when running locally: Agent `http://localhost:8086/docs` · Knowledge `http://localhost:8085/docs`

---

## Agent Service (`localhost:8086`)

### `GET /health`
Liveness check. Returns `200 { "status": "ok" }`.

---

### `POST /chat`

Submit a chat message.

**Headers:** `X-API-KEY` · `user-id`

| Field | Type | Required | Description |
|---|---|---|---|
| `question` | string | ✓ | User's message |
| `conversation_id` | string | | Omit to start a new conversation |
| `model_id` | string | ✓ | Bedrock model ID |
| `knowledge_group_ids` | string[] | | Omit for LLM-only response |

**Response:** `202 Accepted`
```json
{ "conversation_id": "uuid", "message_id": "uuid", "status": "queued" }
```

---

### `GET /conversations/{conversationId}`

Poll for conversation state. **Use this to check for completion** — poll until the latest `AssistantMessage` has `status: completed` or `error`.

**Headers:** `X-API-KEY` · `user-id`

---

### `POST /feedback`

Submit user feedback on a response.

**Headers:** `X-API-KEY` · `user-id`

| Field | Type | Required | Description |
|---|---|---|---|
| `conversation_id` | string | ✓ | |
| `message_id` | string | ✓ | |
| `rating` | string | ✓ | `positive` or `negative` |
| `comment` | string | | Optional free-text |

---

## Knowledge Service (`localhost:8085`)

### `GET /health`
Liveness check.

---

### Knowledge Groups

| Method | Path | Description |
|---|---|---|
| `POST` | `/knowledge-groups` | Create a knowledge group |
| `GET` | `/knowledge-groups` | List user's groups |
| `GET` | `/knowledge-groups/{id}` | Get single group |
| `DELETE` | `/knowledge-groups/{id}` | Delete group |

**Create body:**

| Field | Type | Required |
|---|---|---|
| `name` | string | ✓ |
| `description` | string | |
| `information_asset_owner` | string | |

---

### Documents

| Method | Path | Description |
|---|---|---|
| `POST` | `/knowledge-groups/{id}/documents` | Register document for ingestion |
| `GET` | `/knowledge-groups/{id}/documents` | List documents |
| `GET` | `/knowledge-groups/{id}/documents/{docId}` | Get document + ingest status |
| `DELETE` | `/knowledge-groups/{id}/documents/{docId}` | Remove document |

**Status values:** `not_started` · `in_progress` · `ready` · `failed`

---

### `POST /rag/search`

Semantic search (called by Agent — not typically called directly).

**Headers:** `X-API-KEY`

| Field | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✓ | User question (embedding generated internally) |
| `knowledge_group_ids` | string[] | ✓ | Groups to search |
| `max_results` | int | | Max chunks to return |

---

## Bedrock Stub Admin (`localhost:8090`)

| Method | Path | Description |
|---|---|---|
| `GET` | `/__admin/health` | Health check |
| `GET` | `/__admin/mappings` | View stub mappings |
| `GET` | `/__admin/requests` | Request history |
| `POST` | `/__admin/mappings` | Add mapping dynamically |
