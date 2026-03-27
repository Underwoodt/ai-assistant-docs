# Developer Documentation

[Back to Project Root](../README.md)

---

## Contents

### Architecture & Design
- [Architecture](./ARCHITECTURE.md) — Service topology, sequence diagrams, data flows (from ADR-004)
- [Design Patterns](./DESIGN-PATTERNS.md) — RAG pipeline, async queue, chunking, CDP Uploader integration

### Data
- [Data Models](./DATA-MODELS.md) — Agent and Knowledge Service data models with class diagrams

### APIs
- [REST Endpoints](./APIs/REST-ENDPOINTS.md) — All endpoints across Frontend, Agent, and Knowledge services
- [Authentication](./APIs/AUTH.md) — Entra ID auth flow, user-id header propagation

### Services
- [Agent Service](./services/agent.md) — Chat API, LLM routing, SQS worker, conversation persistence
- [Knowledge Service](./services/knowledge.md) — Document ingest pipeline, embeddings, vector search
- [Frontend Service](./services/frontend.md) — Chat UI, upload flow, session caching

### Operations
- [Setup & Local Development](./SETUP.md) — Prerequisites, per-service setup, verification steps
- [Testing](./TESTING.md) — Unit, integration, journey, accessibility, and performance tests
- [Deployment](./DEPLOYMENT.md) — CDP deployment, infrastructure topology, CI/CD
- [Troubleshooting](./TROUBLESHOOTING.md) — Common failures and fixes
