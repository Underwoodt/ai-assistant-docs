# Developer Documentation

[← Back to Index](../README.md)

Technical reference for engineers working on, deploying, or adopting the AI DEFRA Search codebase.

← [Back to root](../README.md)

---

## Core Guides

| Document | Description |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | System overview diagram, service responsibilities, all data flow sequences (chat, conversation, RAG, upload, ingest), auth layers |
| [DATA-MODELS.md](DATA-MODELS.md) | Agent and Knowledge service data models — Conversation, Message, Source, Document, KnowledgeGroup, KnowledgeVector |
| [SETUP.md](SETUP.md) | Prerequisites, full-stack local setup via core repo, per-service setup, environment variables, verification steps |
| [DESIGN-PATTERNS.md](DESIGN-PATTERNS.md) | Key patterns used: RAG pipeline, async queue, AI-agnostic LLM integration, 12-factor config, layered testing |
| [TESTING.md](TESTING.md) | Test strategy per service, how to run tests, CI/CD quality gates, Python and JavaScript testing standards |
| [DEPLOYMENT.md](DEPLOYMENT.md) | CDP deployment, container images, infrastructure dependencies, CI/CD pipeline, migrations, monitoring |
| [TROUBLESHOOTING.md](TROUBLESHOOTING.md) | Common failures and fixes — frontend, chat queue, knowledge search, Bedrock, auth, database connections |

---

## Services

Per-service deep-dives covering purpose, key modules, data flows, configuration, and how to run.

| Document | Service | Stack |
|---|---|---|
| [services/agent.md](services/agent.md) | Agent Service | Python 3.12+, FastAPI, MongoDB, SQS |
| [services/knowledge.md](services/knowledge.md) | Knowledge Service | Python 3.13+, FastAPI, pgvector, MongoDB |
| [services/frontend.md](services/frontend.md) | Frontend Service | Node.js 22+, Hapi, Nunjucks, Redis |
| [services/bedrock-stub.md](services/bedrock-stub.md) | AWS Bedrock Stub | WireMock — local dev only |

---

## APIs

| Document | Description |
|---|---|
| [APIs/REST-ENDPOINTS.md](APIs/REST-ENDPOINTS.md) | All REST endpoints for Agent and Knowledge services — request/response schemas, status codes |
| [APIs/AUTH.md](APIs/AUTH.md) | Authentication layers — Microsoft Entra ID (users), API key (service-to-service), AWS IAM |
