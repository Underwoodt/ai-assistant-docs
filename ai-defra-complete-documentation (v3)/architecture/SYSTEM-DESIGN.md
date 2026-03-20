# System Design

**High-level architecture of AI DEFRA Search**

---

## Architecture Diagram

```
┌─────────────────────────────────────┐
│   User Browser                      │
└────────────────┬────────────────────┘
                 │
         ┌───────▼────────┐
         │ Frontend       │
         │ React (3000)   │
         └───────┬────────┘
                 │
         ┌───────▼────────┐
         │ Traefik        │
         │ (8080)         │
         └───┬────────┬───┘
             │        │
    ┌────────▼──┐ ┌───▼──────┐
    │   Agent   │ │Knowledge │
    │   8086    │ │   8085   │
    └────┬──────┘ └────┬─────┘
         │             │
    ┌────▼──────────────▼──────┐
    │   Databases & Storage    │
    │ • PostgreSQL + pgvector  │
    │ • MongoDB                │
    │ • Redis                  │
    │ • S3/LocalStack          │
    └──────────────────────────┘
```

---

## Technology Stack

### Backend
- FastAPI (Python 3.12+)
- Uvicorn
- Pydantic for validation

[Source: ai-defra-search-agent/pyproject.toml](https://github.com/DEFRA/ai-defra-search-agent/blob/main/pyproject.toml)
[Source: ai-defra-search-knowledge/pyproject.toml](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/pyproject.toml)

### Frontend
- React
- JavaScript/TypeScript
- Node.js

[Source: ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend)

### Databases
- PostgreSQL + pgvector (vectors)
- MongoDB (metadata)
- Redis (cache)

### Infrastructure
- Docker & Docker Compose
- Traefik (reverse proxy)
- LocalStack (AWS mock)

---

## Service Responsibilities

### Frontend (Port 3000)
- User interface
- Chat input/display
- Session management
- File upload UI

### Agent (Port 8086)
- Chat message handling
- LLM orchestration
- Conversation memory
- RAG integration

### Knowledge (Port 8085)
- Document ingestion
- Text extraction
- Vector embedding
- Semantic search

---

## Data Storage

### PostgreSQL
- Vector embeddings
- Chunk metadata
- Document references

### MongoDB
- Chat conversations
- User feedback
- Application metadata

### Redis
- Session cache
- Request deduplication
- Performance cache

### S3/LocalStack
- Original documents
- Processed files

---

## ⚠️ Unverified Architecture Aspects

- ⚠️ **Scaling strategy** - Not documented
- ⚠️ **Auto-scaling configuration** - Not found
- ⚠️ **Service mesh** - Not implemented
- ⚠️ **Advanced resilience patterns** - Partial

---

## See Also

- [DATA-FLOWS.md](./DATA-FLOWS.md) - How data moves
- [SERVICE-INTERACTIONS.md](./SERVICE-INTERACTIONS.md) - Service communication
