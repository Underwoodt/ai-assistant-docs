# Service Interactions

**How services communicate with each other**

---

## Service Dependencies

```
Frontend
   ↓ HTTP
Agent (8086)
   ↓ HTTP
Knowledge (8085)
   ├─ PostgreSQL
   ├─ MongoDB
   ├─ Redis
   └─ S3/LocalStack
```

---

## Frontend → Agent

**Protocol:** HTTP/REST  
**Port:** 8086

[Source: ai-defra-search-agent/app/](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/)

### Endpoints Called
- POST /chat - Send message
- GET /health - Health check

### Response Format
```json
{
  "response": "string",
  "session_id": "string",
  "sources": ["string"]
}
```

⚠️ **UNVERIFIED:** Exact schema may vary

---

## Agent → Knowledge

**Protocol:** HTTP/REST  
**Port:** 8085

[Source: ai-defra-search-agent/app/common/http_client.py](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/common/http_client.py)

Uses: httpx AsyncClient

### Endpoints Called
- POST /rag/search - Semantic search

---

## Agent → Bedrock

**Protocol:** AWS SDK (boto3)  
**Port:** 4566 (mock) or AWS endpoint

### Operations
- InvokeModel - Generate text
- InvokeModelWithResponseStream - Streaming responses

⚠️ **UNVERIFIED:** Specific retry/timeout policies not documented

---

## Resilience Patterns

### ✅ Verified

**Retry Logic**
[Source: ai-defra-search-agent/app/common/mongo.py#L28-L37](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/common/mongo.py)

**Connection Timeouts**
[Source: ai-defra-search-knowledge/app/common/postgres.py#L62](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/app/common/postgres.py)

### ⚠️ Not Verified

- ❌ Circuit breaker between services
- ❌ Bulkhead isolation
- ❌ Service-to-service retry policies
- ❌ Fallback mechanisms

---

## See Also

- [SYSTEM-DESIGN.md](./SYSTEM-DESIGN.md)
- [DATA-FLOWS.md](./DATA-FLOWS.md)
- [../analysis/DEVELOPER-DOCUMENTATION-COMPLETE.md](../analysis/DEVELOPER-DOCUMENTATION-COMPLETE.md)
