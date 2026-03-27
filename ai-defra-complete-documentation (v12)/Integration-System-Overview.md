# System Overview

> 🔧 **Integration & Deployment** — [Home](Home) › [Integration & Deployment](Home#integration--deployment)

---

## Services

| Service | Technology | Responsibility | Port (local) |
|---|---|---|---|
| **Frontend** | Node.js 22, Hapi, Nunjucks | Chat UI, auth, file upload, async polling | `frontend.localhost` via Traefik |
| **Agent Service** | Python 3.13, FastAPI | LLM inference, conversation state, SQS queue | `8086` |
| **Knowledge Service** | Python 3.12, FastAPI | Ingestion pipeline, embeddings, pgvector search | `8085` |
| **Bedrock Stub** | WireMock | Offline Bedrock replacement (dev/test only) | `8090` (host) / `8080` (Docker) |
| **MongoDB** | v6.0.13 | Agent: conversations + messages · Knowledge: document metadata | `27017` |
| **PostgreSQL + pgvector** | Custom Dockerfile | Vector embeddings (1024-dim) for RAG search | `5432` |
| **Redis** | v7.2.3 | Frontend short-term conversation cache | `6379` |
| **LocalStack** | v4.9.2 | AWS emulation: S3, SQS, SNS, Firehose | `4566` |
| **Traefik** | v3 | Reverse proxy, service routing | `80` |

---

## External Dependencies

| Dependency | Provider | Purpose | Required in |
|---|---|---|---|
| AWS Bedrock — Claude | Amazon Web Services | LLM inference | Production, CDP |
| AWS Bedrock — Titan Embed v2 | Amazon Web Services | Text embeddings (`amazon.titan-embed-text-v2:0`) | Production, CDP |
| AWS S3 | Amazon Web Services | Uploaded file storage | Production, CDP |
| AWS SQS | Amazon Web Services | Chat message queue | Production, CDP |
| AWS CloudWatch | Amazon Web Services | Metrics and monitoring | Production, CDP |
| Microsoft Entra ID | Microsoft Azure | User authentication (OAuth 2.0) | Production, CDP |
| CDP Uploader | DEFRA (managed) | Virus-checked file upload to S3 | Production, CDP |

---

## Service Boundaries

```
Frontend
  ├── → Agent Service      POST /chat, GET /conversations/{id}
  ├── → Knowledge Service  POST /knowledge-groups, POST /documents, GET ingest status
  ├── → CDP Uploader       POST /initiate
  └── ← CDP Uploader       callback (upload complete)

Agent Service
  ├── → Knowledge Service  POST /rag/search
  ├── → AWS Bedrock        converse, embed
  ├── ↔ AWS SQS            enqueue + worker poll
  └── ↔ MongoDB            conversations, messages

Knowledge Service
  ├── → AWS Bedrock        Titan Embed (embed chunks)
  ├── → AWS S3             fetch uploaded files
  ├── ↔ MongoDB            knowledge groups, documents
  └── ↔ PostgreSQL         vector embeddings
```
