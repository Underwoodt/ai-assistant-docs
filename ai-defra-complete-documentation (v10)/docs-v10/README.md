# AI DEFRA Search — Documentation

Documentation for the AI DEFRA Search Assistant — a reference RAG chat assistant built on DEFRA's Core Delivery Platform (CDP).

---

## Start Here

| Document | Audience | Description |
|---|---|---|
| [High-Level Overview](HIGH_LEVEL_OVERVIEW.md) | Everyone | What was built, why, what it does, limitations, and future work |

---

## Documentation Tracks

| Track | Directory | Audience |
|---|---|---|
| **Developer Docs** | [docs-developer/](docs-developer/README.md) | Engineers working on or integrating with the system |
| **Stakeholder Docs** | [docs-stakeholder/](docs-stakeholder/README.md) | Product owners, business stakeholders, non-technical audiences |
| **Integration & Deployment** | [docs-integration/](docs-integration/README.md) | Integration engineers, DevOps, platform teams |

---

## Quick Links

### For Developers
- [Architecture](docs-developer/ARCHITECTURE.md) — component diagram, conversation flows, ADR sequence diagrams
- [Setup](docs-developer/SETUP.md) — get the full stack running locally in minutes
- [REST Endpoints](docs-developer/APIs/REST-ENDPOINTS.md) — API reference for all services
- [Data Models](docs-developer/DATA-MODELS.md) — MongoDB and PostgreSQL schemas

### For Stakeholders
- [Features](docs-stakeholder/FEATURES.md) — what users can do
- [How It Works](docs-stakeholder/ARCHITECTURE-SIMPLE.md) — plain-English system explanation
- [Considerations for Adopting Teams](docs-stakeholder/ROADMAP.md) — what teams need to address
- [FAQ](docs-stakeholder/FAQ.md) — common questions answered

### For Integration & DevOps
- [Service Interactions](docs-integration/SERVICE-INTERACTIONS.md) — sequence diagrams for all flows
- [Deployment Topology](docs-integration/DEPLOYMENT-TOPOLOGY.md) — containers, ports, networking
- [Environments](docs-integration/ENVIRONMENTS.md) — local vs CDP, config differences

---

## Repositories

| Repo | Type | Purpose |
|---|---|---|
| [ai-defra-search-core](https://github.com/DEFRA/ai-defra-search-core) | Meta-repo | Orchestrates all services for local development |
| [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent) | Python/FastAPI | LLM inference, conversation state, RAG orchestration |
| [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge) | Python/FastAPI | Document ingestion, embeddings, semantic search |
| [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend) | Node.js/Hapi | Chat UI, auth, file upload |
| [ai-defra-search-aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub) | WireMock | Offline Bedrock replacement for local dev |
| [ai-defra-search-journey-tests](https://github.com/DEFRA/ai-defra-search-journey-tests) | WebdriverIO | End-to-end and accessibility tests |
| [ai-defra-search-perf-tests](https://github.com/DEFRA/ai-defra-search-perf-tests) | Apache JMeter | Load and performance tests |

---

*Licensed under the Open Government Licence v3*
