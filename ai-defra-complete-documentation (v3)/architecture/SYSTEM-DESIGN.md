# System Design

**High-level architecture of AI DEFRA Search**

> ✅ This file has been verified and corrected against source repositories.
> 🔴 = was wrong in previous version | 🟢 = confirmed from source

---

## Architecture Diagram

```
┌─────────────────────────────────────┐
│ User Browser                        │
└────────────────┬────────────────────┘
                 │ http://frontend.localhost
         ┌───────▼────────┐
         │   Traefik      │
         │ (reverse proxy)│
         └───┬────────────┘
             │
         ┌───▼────────────────┐
         │ Frontend           │
         │ Node.js/Hapi       │  🔴 was "React (3000)"
         │ (Port 3000)        │
         └───────┬────────────┘
                 │ HTTP
         ┌───────▼────────┐
         │     Agent      │
         │   (Port 8086)  │
         └───┬────────────┘
             │ HTTP
         ┌───▼────────────┐
         │   Knowledge    │
         │   (Port 8085)  │
         └───┬────────────┘
             │
    ┌────────▼──────────────┐
    │  Databases & Storage  │
    │ • PostgreSQL+pgvector │
    │ • MongoDB             │
    │ • Redis               │
    │ • S3/LocalStack       │
    └───────────────────────┘
```

> **Access URL:** Always use `http://frontend.localhost` (via Traefik). Using `localhost:3000` directly breaks AWS file upload routing.
> Source: [ai-defra-search-frontend README](https://github.com/DEFRA/ai-defra-search-frontend#readme)

---

## Technology Stack

### Backend Services

| Service | Framework | Language | Python Version |
|---|---|---|---|
| Agent | FastAPI + Uvicorn | Python | **≥ 3.13.7** 🔴 was "3.12+" |
| Knowledge | FastAPI + Uvicorn | Python | **≥ 3.12** |

Sources:
- [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)
- [ai-defra-search-knowledge README](https://github.com/DEFRA/ai-defra-search-knowledge#readme)

### Frontend 🔴 Corrected — was documented as React

The frontend is a **server-rendered Node.js application**, not a React SPA.

| Component | Technology |
|---|---|
| Runtime | Node.js |
| Web framework | **Hapi** |
| Templating | **Nunjucks** (server-side rendering) |
| Styling | SCSS |
| Test runner | Vitest |
| Session cache | CatboxRedis (production) / CatboxMemory (local) |

Source: [ai-defra-search-frontend README](https://github.com/DEFRA/ai-defra-search-frontend#readme)

### Infrastructure

| Component | Technology | Purpose |
|---|---|---|
| Orchestration | Docker + Docker Compose | Run all services together |
| Reverse proxy | Traefik | Route `frontend.localhost` → Frontend (port 3000) |
| AWS mock | LocalStack (port 4566) | S3 and SQS locally |
| Bedrock mock | WireMock ([aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub)) | Claude + Titan Embed APIs offline |
| Package manager | uv | Python dependency management |

### Databases & Storage

| Store | Technology | Used by | Purpose |
|---|---|---|---|
| Vectors | PostgreSQL + pgvector | Knowledge | Embeddings + chunk metadata |
| Conversations | MongoDB | Agent | Chat history, feedback |
| Cache | Redis | Frontend, Agent | Session cache, deduplication |
| Object storage | S3 / LocalStack | Frontend, Knowledge | Document files, uploads |

---

## Service Responsibilities

### Frontend (Port 3000 → `frontend.localhost`)
- Server-rendered chat UI (Nunjucks templates)
- File upload (via S3/SQS with LocalStack or CDP uploader)
- Model selection UI
- Conversation caching (Redis in production, in-memory locally)
- Session TTL: 4 hours default

Source: [ai-defra-search-frontend README](https://github.com/DEFRA/ai-defra-search-frontend#readme)

### Agent (Port 8086)
- Chat message handling (`POST /chat`)
- LLM orchestration via AWS Bedrock (boto3)
- Conversation memory in MongoDB
- RAG integration via Knowledge service
- User feedback (`POST /feedback`)

Source: [ai-defra-search-agent README](https://github.com/DEFRA/ai-defra-search-agent#readme)

### Knowledge (Port 8085)
- Document ingestion and vector embedding
- Semantic search (RAG) via pgvector
- Embeddings via AWS Bedrock (Titan Embed)
- Knowledge group management

> ⚠️ **Work in progress** — production API endpoints are not yet publicly documented. See [KNOWLEDGE-API.md](../api-reference/KNOWLEDGE-API.md) for current status.

Source: [ai-defra-search-knowledge README](https://github.com/DEFRA/ai-defra-search-knowledge#readme)

---

## ⚠️ Unverified / Not Yet Documented

- ⚠️ **Scaling strategy** — not documented in any source repo
- ⚠️ **Auto-scaling configuration** — not found
- ⚠️ **Service mesh** — not implemented
- ⚠️ **Advanced resilience patterns** — partial (see SERVICE-INTERACTIONS.md)

---

## See Also

- [DATA-FLOWS.md](./DATA-FLOWS.md)
- [SERVICE-INTERACTIONS.md](./SERVICE-INTERACTIONS.md)
- [../api-reference/AGENT-API.md](../api-reference/AGENT-API.md)
- [../api-reference/KNOWLEDGE-API.md](../api-reference/KNOWLEDGE-API.md)
