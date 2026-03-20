# Agent API Documentation

**Chat API for AI DEFRA Search**

Port: 8086

---

## Endpoints

### POST /chat

Send a chat message

[Source: ai-defra-search-agent/app/entrypoints/](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/entrypoints/)

**Request:**
```json
{
  "message": "Your question here",
  "session_id": "optional-session-id",
  "knowledge_group_id": "optional-group-id"
}
```

**Response:**
```json
{
  "response": "AI assistant response",
  "session_id": "session-id",
  "sources": ["document1", "document2"]
}
```

⚠️ **UNVERIFIED:** Exact response schema not found in code

### GET /health

Health check endpoint

**Response:**
```json
{
  "status": "ok"
}
```

### POST /feedback

Submit user feedback

⚠️ **UNVERIFIED:** Feedback endpoint implementation not verified

---

## Authentication

❌ **NOT FOUND IN SOURCE**

- No API key mechanism found
- No authentication middleware documented

---

## Rate Limiting

⚠️ **UNVERIFIED:** Rate limiting configuration not found in source

---

## Timeouts

[Source: ai-defra-search-agent/app/config.py](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/config.py)

- Request timeout: Configurable via environment
- Database timeout: Configurable
- Bedrock timeout: Configurable

---

## See Also

- [EXAMPLES.md](./EXAMPLES.md) - Working code examples
- [../troubleshooting/DEBUGGING.md](../troubleshooting/DEBUGGING.md) - Debug issues
