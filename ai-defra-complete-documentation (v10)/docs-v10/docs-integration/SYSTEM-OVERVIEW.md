# System Overview

[← Back to Integration Docs](README.md)

---

## Services and Technologies

| Service | Technology | Responsibility | Port (local) |
|---|---|---|---|
| **Frontend** | Node.js 22, Hapi, Nunjucks | Chat UI, auth, file upload, async polling | `3000` (via Traefik: `frontend.localhost`) |
| **Agent Service** | Python 3.13, FastAPI, Uvicorn | LLM inference, conversation state, SQS queue | `8086` |
| **Knowledge Service** | Python 3.12, FastAPI, Uvicorn | Ingestion pipeline, embeddings, pgvector search | `8085` |
| **Bedrock Stub** | WireMock | Offline Bedrock replacement (dev/test only) | `8090` (ext) / `8080` (Docker net) |
| **MongoDB** | v6.0.13 | Agent: conversations + messages · Knowledge: document metadata | `27017` |
| **PostgreSQL + pgvector** | Custom Dockerfile | Vector embeddings (1024-dim) for RAG search | `5432` |
| **Redis** | v7.2.3 | Frontend short-term conversation cache | `6379` |
| **LocalStack** | v4.9.2 | AWS emulation: S3, SQS, SNS, Firehose | `4566` |
| **Traefik** | v3 | Reverse proxy and service routing | `80` / `443` |

---

## External Dependencies

| Dependency | Provider | Purpose | Required in |
|---|---|---|---|
| AWS Bedrock (Claude) | Amazon Web Services | LLM inference | Production, CDP |
| AWS Bedrock (Titan Embed v2) | Amazon Web Services | Text embeddings (`amazon.titan-embed-text-v2:0`) | Production, CDP |
| AWS S3 | Amazon Web Services | Uploaded file storage | Production, CDP |
| AWS SQS | Amazon Web Services | Chat message queue | Production, CDP |
| AWS CloudWatch | Amazon Web Services | Metrics and monitoring | Production, CDP |
| Microsoft Entra ID | Microsoft Azure | User authentication (OAuth 2.0) | Production, CDP |
| CDP Uploader | DEFRA (managed) | Virus-checked file upload to S3 | Production, CDP |
| BrowserStack | BrowserStack | Cross-browser journey testing | CI/CD (optional) |

---

## Service Boundaries

Each service is independently deployable as a Docker container. Service-to-service communication is HTTP/REST with API key authentication.

```
Frontend
  ├── Calls: Agent Service (POST /chat, GET /conversations/{id})
  ├── Calls: Knowledge Service (POST /knowledge-groups, POST /documents, GET ingest status)
  ├── Calls: CDP Uploader (POST /initiate)
  └── Receives callback from: CDP Uploader (file upload complete)

Agent Service
  ├── Calls: Knowledge Service (POST /rag/search)
  ├── Calls: AWS Bedrock (converse, embed)
  ├── Reads/writes: MongoDB (conversations, messages)
  └── Reads/writes: AWS SQS (enqueue + worker poll)

Knowledge Service
  ├── Calls: AWS Bedrock Titan Embed (embed chunks)
  ├── Reads: AWS S3 (fetch uploaded files)
  ├── Reads/writes: MongoDB (knowledge groups, documents)
  └── Reads/writes: PostgreSQL pgvector (embeddings)
```
