# AI DEFRA Search — Documentation

AI-powered RAG chat assistant built on DEFRA's Core Delivery Platform (CDP). A reference implementation for any CDP team looking to deploy similar AI features.

---

## Start Here

[High-Level Overview](HIGH_LEVEL_OVERVIEW.md) — what was built, why, what it does, limitations, and future work. Read this first regardless of your role.

---

## Documentation Tracks

| Track | For |
|---|---|
| [Developer Docs](#developer-docs) | Engineers building on or integrating with the system |
| [Stakeholder Docs](#stakeholder-docs) | Product owners, business stakeholders, non-technical audiences |
| [Integration & Deployment](#integration--deployment) | DevOps, platform teams, integration engineers |

---

## Developer Docs

| Page | Description |
|---|---|
| [Architecture](docs-developer/ARCHITECTURE.md) | Component diagrams, conversation flows, all ADR sequence diagrams |
| [Setup](docs-developer/SETUP.md) | Get the full stack running locally |
| [Design Patterns](docs-developer/DESIGN-PATTERNS.md) | RAG, async queue-and-poll, CDP Uploader callback, background ingestion |
| [Data Models](docs-developer/DATA-MODELS.md) | MongoDB and PostgreSQL schemas |
| [Testing](docs-developer/TESTING.md) | Unit, journey, accessibility, and performance test guides |
| [Deployment](docs-developer/DEPLOYMENT.md) | CDP pipeline, environments, secrets, monitoring |
| [Troubleshooting](docs-developer/TROUBLESHOOTING.md) | Common failures and fixes |

**Services**

| Page | Stack |
|---|---|
| [Agent Service](docs-developer/services/agent.md) | Python / FastAPI / MongoDB / SQS |
| [Knowledge Service](docs-developer/services/knowledge.md) | Python / FastAPI / PostgreSQL pgvector / MongoDB |
| [Frontend Service](docs-developer/services/frontend.md) | Node.js / Hapi / Nunjucks / Redis |
| [Bedrock Stub](docs-developer/services/bedrock-stub.md) | WireMock — offline Bedrock replacement |

**APIs**

| Page | Description |
|---|---|
| [REST Endpoints](docs-developer/APIs/REST-ENDPOINTS.md) | All endpoints with request/response schemas |
| [Authentication](docs-developer/APIs/AUTH.md) | Entra ID, API keys, IAM roles |

---

## Stakeholder Docs

| Page | Description |
|---|---|
| [Overview](docs-stakeholder/OVERVIEW.md) | What the system is and why it was built |
| [Features](docs-stakeholder/FEATURES.md) | What users can do; what's not included |
| [How It Works](docs-stakeholder/ARCHITECTURE-SIMPLE.md) | Plain-English system explanation |
| [Security & Privacy](docs-stakeholder/SECURITY-PRIVACY.md) | Data handling, access controls, AI data usage |
| [Considerations for Adopting Teams](docs-stakeholder/ROADMAP.md) | What teams need to address before going to production |
| [FAQ](docs-stakeholder/FAQ.md) | Common questions answered in plain English |

---

## Integration & Deployment

| Page | Description |
|---|---|
| [System Overview](docs-integration/SYSTEM-OVERVIEW.md) | All services, technologies, responsibilities, external dependencies |
| [Service Interactions](docs-integration/SERVICE-INTERACTIONS.md) | Mermaid sequence diagrams for all flows |
| [Deployment Topology](docs-integration/DEPLOYMENT-TOPOLOGY.md) | Containers, ports, networking, database ownership |
| [Environments](docs-integration/ENVIRONMENTS.md) | Local vs CDP differences, config variables |
| [Shared Libraries & Contracts](docs-integration/SHARED-LIBRARIES.md) | API contracts, shared conventions, tooling patterns |

---

## Repositories

| Repo | Purpose |
|---|---|
| [ai-defra-search-core](https://github.com/DEFRA/ai-defra-search-core) | Meta-repo: orchestrates all services locally |
| [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent) | Agent Service |
| [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge) | Knowledge Service |
| [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend) | Frontend |
| [ai-defra-search-aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub) | Bedrock Stub |
| [ai-defra-search-journey-tests](https://github.com/DEFRA/ai-defra-search-journey-tests) | Journey & Accessibility Tests |
| [ai-defra-search-perf-tests](https://github.com/DEFRA/ai-defra-search-perf-tests) | Performance Tests |

---

*Licensed under the Open Government Licence v3*
