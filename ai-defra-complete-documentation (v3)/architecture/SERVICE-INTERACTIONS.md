# Service Interactions

**How services communicate with each other**

> ✅ Verified and corrected against source repositories.
> 🔴 = was wrong in previous version | 🟢 = confirmed from source

---

## Service Dependency Chain

```
User Browser
  ↓ http://frontend.localhost
Traefik (reverse proxy)
  ↓ HTTP → Port 3000
Frontend (Node.js / Hapi / Nunjucks)   🔴 was "React"
  ↓ HTTP → Port 8086
Agent (FastAPI / Python 3.13+)
  ↓ HTTP → Port 8085
Knowledge (FastAPI / Python 3.12+)
  ├─ PostgreSQL + pgvector
  ├─ MongoDB
  ├─ Redis
  └─ S3 / LocalStack
Agent
  └─ AWS Bedrock (or WireMock stub at Port 4566)
```

---

## Frontend → Agent

**Protocol:** HTTP/REST
**Port:** 8086

Source: [ai-defra-search-agent README — API Endpoints](https://github.com/DEFRA/ai-defra-search-agent#readme)

### Endpoints Called

| Method | Path | Purpose |
|---|---|---|
| POST | `/chat` | Submit a question to the AI assistant |
| GET | `/health` | Health check |

### POST /chat — Request Body 🔴 Corrected

```json
{
  "question": "What is the permitted development policy?",
  "model_id": "anthropic.claude-3-haiku"
}
```

> 🔴 **Correction:** The previous version documented `"message"` and `"session_id"` as fields. The Agent README curl example confirms the actual fields are `"question"` and `"model_id"`.
> Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

### POST /chat — Response

```json
{
  "response": "string",
  "session_id": "string",
  "sources": ["string"]
}
```

> ⚠️ **UNVERIFIED:** Exact response schema not confirmed in source code. The README documents request shape only.

---

## Agent → Knowledge

**Protocol:** HTTP/REST
**Port:** 8085

Source: [ai-defra-search-agent app/common/http_client.py](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/common/http_client.py)

Uses: `httpx` AsyncClient

### Endpoints Called

| Method | Path | Purpose |
|---|---|---|
| POST | `/rag/search` | Semantic search against knowledge store |

> ⚠️ **UNVERIFIED:** The `/rag/search` endpoint is referenced in the Agent but the Knowledge service README only documents placeholder/example endpoints (the service is work-in-progress). The endpoint likely exists in the Knowledge service code but is not yet in its public documentation.
> See: [KNOWLEDGE-API.md](../api-reference/KNOWLEDGE-API.md)

---

## Agent → AWS Bedrock

**Protocol:** AWS SDK (boto3)
**Endpoint:** LocalStack port 4566 (local) or AWS regional endpoint (production)

Source: [ai-defra-search-agent README — Environment Variables](https://github.com/DEFRA/ai-defra-search-agent#readme)

### Operations Used

| Operation | Purpose |
|---|---|
| `InvokeModel` | Generate text responses (synchronous) |

> 🔴 **Correction:** The previous version documented `InvokeModelWithResponseStream` for streaming. **The system does not implement streaming.** Chat responses are synchronous. `InvokeModel` is the only confirmed operation.

### Key Bedrock Environment Variables

| Variable | Purpose |
|---|---|
| `AWS_REGION` | AWS region (default: `eu-central-1`) |
| `AWS_BEDROCK_DEFAULT_GENERATION_MODEL` | Model used for generation |
| `AWS_BEDROCK_AVAILABLE_GENERATION_MODELS` | JSON array of selectable models |
| `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` | Credentials (use `"test"` locally) |

Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

---

## Frontend → S3 / SQS (File Uploads)

**Protocol:** AWS SDK via LocalStack (local) or CDP uploader (deployed)

Source: [ai-defra-search-frontend README](https://github.com/DEFRA/ai-defra-search-frontend#readme)

The Frontend uses SQS queues and S3 buckets for document upload processing. Locally, LocalStack mocks these AWS services. In the CDP environment, a stub image handles upload callbacks.

> ⚠️ **UNVERIFIED:** The exact SQS message schema and S3 bucket structure are not documented in the Frontend README. Check the Frontend service configuration for `SQS_*` and `S3_*` environment variables.

---

## Resilience Patterns

### ✅ Verified

| Pattern | Location |
|---|---|
| MongoDB retry logic | [ai-defra-search-agent app/common/mongo.py](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/common/mongo.py) |
| PostgreSQL connection timeouts | [ai-defra-search-knowledge app/common/postgres.py](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/app/common/postgres.py) |

### ⚠️ Not Verified / Not Implemented

- ❌ Circuit breakers between services — not documented
- ❌ Bulkhead isolation — not documented
- ❌ Service-to-service retry policies — not documented
- ❌ Fallback mechanisms — not documented
- ❌ Bedrock retry/timeout policies — not documented

---

## See Also

- [SYSTEM-DESIGN.md](./SYSTEM-DESIGN.md)
- [DATA-FLOWS.md](./DATA-FLOWS.md)
- [../api-reference/AGENT-API.md](../api-reference/AGENT-API.md)
- [../api-reference/KNOWLEDGE-API.md](../api-reference/KNOWLEDGE-API.md)
