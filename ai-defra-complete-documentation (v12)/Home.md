# AI DEFRA Search — Documentation

AI-powered RAG chat assistant built on DEFRA's Core Delivery Platform (CDP). A reference implementation for any CDP team looking to deploy similar AI features.

---

## Start Here

[High-Level Overview](High-Level-Overview) — what was built, why, what it does, limitations, and future work. Read this first regardless of your role.

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
| [Architecture](Developer-Architecture) | Component diagrams, conversation flows, all ADR sequence diagrams |
| [Setup](Developer-Setup) | Get the full stack running locally |
| [Design Patterns](Developer-Design-Patterns) | RAG, async queue-and-poll, CDP Uploader callback, background ingestion |
| [Data Models](Developer-Data-Models) | MongoDB and PostgreSQL schemas |
| [Testing](Developer-Testing) | Unit, journey, accessibility, and performance test guides |
| [Deployment](Developer-Deployment) | CDP pipeline, environments, secrets, monitoring |
| [Troubleshooting](Developer-Troubleshooting) | Common failures and fixes |

**Services**

| Page | Stack |
|---|---|
| [Agent Service](Developer-Services-Agent) | Python / FastAPI / MongoDB / SQS |
| [Knowledge Service](Developer-Services-Knowledge) | Python / FastAPI / PostgreSQL pgvector / MongoDB |
| [Frontend Service](Developer-Services-Frontend) | Node.js / Hapi / Nunjucks / Redis |
| [Bedrock Stub](Developer-Services-Bedrock-Stub) | WireMock — offline Bedrock replacement |

**APIs**

| Page | Description |
|---|---|
| [REST Endpoints](Developer-API-REST-Endpoints) | All endpoints with request/response schemas |
| [Authentication](Developer-API-Auth) | Entra ID, API keys, IAM roles |

---

## Stakeholder Docs

| Page | Description |
|---|---|
| [Overview](Stakeholder-Overview) | What the system is and why it was built |
| [Features](Stakeholder-Features) | What users can do; what's not included |
| [How It Works](Stakeholder-How-It-Works) | Plain-English system explanation |
| [Security & Privacy](Stakeholder-Security-Privacy) | Data handling, access controls, AI data usage |
| [Considerations for Adopting Teams](Stakeholder-Adopting-Teams) | What teams need to address before going to production |
| [FAQ](Stakeholder-FAQ) | Common questions answered in plain English |

---

## Integration & Deployment

| Page | Description |
|---|---|
| [System Overview](Integration-System-Overview) | All services, technologies, responsibilities, external dependencies |
| [Service Interactions](Integration-Service-Interactions) | Mermaid sequence diagrams for all flows |
| [Deployment Topology](Integration-Deployment-Topology) | Containers, ports, networking, database ownership |
| [Environments](Integration-Environments) | Local vs CDP differences, config variables |
| [Shared Libraries & Contracts](Integration-Shared-Libraries) | API contracts, shared conventions, tooling patterns |

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
