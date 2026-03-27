# REST Endpoints

[← Back to Developer Docs](../README.md)

> Full interactive docs available at Swagger UI when services are running locally.
> Agent: `http://localhost:8086/docs` · Knowledge: `http://localhost:8085/docs`

---

## Agent Service (`localhost:8086`)

### Health

| Method | Path | Description |
|---|---|---|
| `GET` | `/health` | Liveness check |

**Response:** `200 OK` `{ "status": "ok" }`

---

### Chat

#### Start / Submit a Chat Message

```
POST /chat
```

**Headers:** `X-API-KEY: <key>` · `user-id: <user-uuid>`

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `question` | string | ✓ | User's message |
| `conversation_id` | string | | Existing conversation UUID (omit to start new) |
| `model_id` | string | ✓ | Bedrock model identifier (e.g. `anthropic.claude-3-5-sonnet-20241022-v2:0`) |
| `knowledge_group_ids` | string[] | | Knowledge groups to search; omit for LLM-only response |

**Response:** `202 Accepted`

```json
{
  "conversation_id": "uuid",
  "message_id": "uuid",
  "status": "queued"
}
```

---

### Conversations

#### Get Conversation

```
GET /conversations/{conversationId}
```

**Headers:** `X-API-KEY: <key>` · `user-id: <user-uuid>`

**Response:** `200 OK` — full conversation with all messages and status

**Use this endpoint to poll for completion** — poll until the latest `AssistantMessage` has `status: completed` or `error`.

---

### Feedback

```
POST /feedback
```

**Headers:** `X-API-KEY: <key>` · `user-id: <user-uuid>`

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `conversation_id` | string | ✓ | Conversation to rate |
| `message_id` | string | ✓ | Message to rate |
| `rating` | string | ✓ | `positive` or `negative` |
| `comment` | string | | Optional free-text feedback |

---

## Knowledge Service (`localhost:8085`)

### Health

| Method | Path | Description |
|---|---|---|
| `GET` | `/health` | Liveness check |

---

### Knowledge Groups

| Method | Path | Description |
|---|---|---|
| `POST` | `/knowledge-groups` | Create a new knowledge group |
| `GET` | `/knowledge-groups` | List all knowledge groups for user |
| `GET` | `/knowledge-groups/{id}` | Get a single knowledge group |
| `DELETE` | `/knowledge-groups/{id}` | Delete a knowledge group |

**Create request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | ✓ | Group name |
| `description` | string | | Optional description |
| `information_asset_owner` | string | | Information asset owner |

---

### Documents

| Method | Path | Description |
|---|---|---|
| `POST` | `/knowledge-groups/{id}/documents` | Register an uploaded document for ingestion |
| `GET` | `/knowledge-groups/{id}/documents` | List documents in a group |
| `GET` | `/knowledge-groups/{id}/documents/{docId}` | Get document and ingest status |
| `DELETE` | `/knowledge-groups/{id}/documents/{docId}` | Remove a document |

**Document status values:** `not_started` · `in_progress` · `ready` · `failed`

---

### RAG Search

```
POST /rag/search
```

> Called by the Agent Service — not typically called directly by the frontend.

**Headers:** `X-API-KEY: <key>`

**Request body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✓ | The user's question (plain text — embedding generated internally) |
| `knowledge_group_ids` | string[] | ✓ | Groups to search |
| `max_results` | int | | Maximum chunks to return (default varies) |

**Response:** Array of matching chunks with content, source metadata, and similarity score.

---

## Bedrock Stub Admin (`localhost:8090`)

| Method | Path | Description |
|---|---|---|
| `GET` | `/__admin/health` | Stub health check |
| `GET` | `/__admin/mappings` | View loaded stub mappings |
| `GET` | `/__admin/requests` | Request history and logs |
| `POST` | `/__admin/mappings` | Add a new stub mapping dynamically |
