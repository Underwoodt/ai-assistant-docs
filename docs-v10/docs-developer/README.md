# Developer Documentation

[← Back to Index](../README.md)

Technical reference for engineers working on or integrating with the AI DEFRA Search Assistant.

---

## Core Guides

| Document | Description |
|---|---|
| [Architecture](ARCHITECTURE.md) | System overview, component interactions, data flows, all ADR diagrams |
| [Setup](SETUP.md) | Prerequisites, local stack setup, environment variables, dev commands |
| [Design Patterns](DESIGN-PATTERNS.md) | RAG, async queue-and-poll, CDP Uploader callback, background ingestion |
| [Data Models](DATA-MODELS.md) | MongoDB and PostgreSQL schemas for Agent and Knowledge services |
| [Testing](TESTING.md) | Unit, integration, journey, accessibility, and performance test guides |
| [Deployment](DEPLOYMENT.md) | CDP deployment, CI/CD pipeline, environments, secrets, monitoring |
| [Troubleshooting](TROUBLESHOOTING.md) | Common failures and fixes |

---

## Services

| Service | Stack | Purpose |
|---|---|---|
| [Agent](services/agent.md) | Python / FastAPI / MongoDB / SQS | LLM inference, conversation state, RAG orchestration |
| [Knowledge](services/knowledge.md) | Python / FastAPI / PostgreSQL pgvector / MongoDB | Document ingestion, embeddings, semantic search |
| [Frontend](services/frontend.md) | Node.js / Hapi / Nunjucks / Redis | Chat UI, auth, file upload, async polling |
| [Bedrock Stub](services/bedrock-stub.md) | WireMock / Docker | Offline Bedrock replacement for local dev |

---

## APIs

| Document | Description |
|---|---|
| [REST Endpoints](APIs/REST-ENDPOINTS.md) | All API endpoints with request/response schemas |
| [Authentication](APIs/AUTH.md) | Entra ID, API keys, IAM roles, local auth setup |
