# AI DEFRA Search — Documentation

> A reference implementation of a Retrieval-Augmented Generation (RAG) chat assistant on DEFRA's Core Delivery Platform (CDP).

---

## Documentation

| Section | Audience | Description |
|---|---|---|
| [High-Level Overview](./HIGH_LEVEL_OVERVIEW.md) | Everyone | What was built, why, capabilities, limitations, and future ideas |
| [Developer Documentation](./docs-developer/README.md) | Engineers | Architecture, APIs, data models, setup, testing, deployment, troubleshooting |

---

## Repositories

| Repo | Type | Purpose |
|---|---|---|
| [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent) | Python / FastAPI | Chat API, LLM routing via Bedrock, SQS async worker |
| [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge) | Python / FastAPI | Document ingest, vector embeddings, RAG search |
| [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend) | Node.js / Hapi | Browser chat UI, file upload, session management |
| [ai-defra-search-journey-tests](https://github.com/DEFRA/ai-defra-search-journey-tests) | WebdriverIO | End-to-end and accessibility tests |
| [ai-defra-search-perf-tests](https://github.com/DEFRA/ai-defra-search-perf-tests) | Apache JMeter | Load and performance tests |
| [ai-defra-search-aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub) | WireMock | Offline development stub for Claude + Titan Embed APIs |

---

## Quick Links

- [Architecture diagrams](./docs-developer/ARCHITECTURE.md)
- [Local setup guide](./docs-developer/SETUP.md)
- [REST API endpoints](./docs-developer/APIs/REST-ENDPOINTS.md)
- [Data models](./docs-developer/DATA-MODELS.md)
- [Troubleshooting](./docs-developer/TROUBLESHOOTING.md)

---

*Licensed under the Open Government Licence v3. Contains public sector information licensed under the Open Government licence v3.*
