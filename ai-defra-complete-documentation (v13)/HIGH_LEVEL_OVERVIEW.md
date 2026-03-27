# High-Level Overview

[← Back to Index](README.md)



---

## 1. What We Built

The AI DEFRA Search Assistant is a reference implementation of a Retrieval-Augmented Generation (RAG) chat assistant, built and deployed on DEFRA's Core Delivery Platform (CDP). It combines conversational AI with the ability to query a user's own uploaded documents, delivering a browser-based assistant that any CDP team can use as a starting point for their own AI features.

The system is composed of four core services and two test suites:

| Component | Type | Role |
|---|---|---|
| **Frontend** | Node.js / Hapi / Nunjucks | Browser-based chat UI; manages user sessions, file uploads, model selection, and conversation caching |
| **Agent Service** | Python / FastAPI | All LLM inference; manages multi-turn conversation history; orchestrates RAG lookups |
| **Knowledge Service** | Python / FastAPI | Document ingestion pipeline; generates vector embeddings; serves semantic search results |
| **Bedrock Stub** | WireMock | Offline stand-in for AWS Bedrock — no cloud account needed for local dev |
| **Journey Tests** | WebdriverIO / Node.js | End-to-end and WCAG accessibility tests covering the main user flows |
| **Performance Tests** | Apache JMeter | Load, stress, and throughput tests against the full stack |

For full technical detail see [Developer Architecture](docs-developer/ARCHITECTURE.md).

---

## 2. Why We Built It

### The Problem

DEFRA teams looking to add AI-powered search had no internal reference to draw on. Deploying a RAG assistant involves a non-trivial combination of cloud AI APIs, vector databases, asynchronous processing, document ingestion pipelines, and authentication — each with their own CDP-specific considerations.

### The Goal

To provide a practical, end-to-end paved road for any DEFRA team that wants to deploy a similar AI assistant on CDP. The project demonstrates this is achievable within DEFRA's existing platform and tooling, and reduces the cost for teams to follow the same pattern.

The project is explicitly a **reference implementation** — it shows one way to do this well, not the only way. Teams are expected to adapt data models, system prompts, and retrieval strategies to their own use cases.

### Key Design Principles

- **CDP-first** — runs entirely on DEFRA's Core Delivery Platform; follows CDP conventions for deployment, CI/CD, and secrets management
- **Offline development** — the Bedrock Stub means developers can run the full stack locally without AWS credentials
- **RAG grounded by default** — user queries are answered from uploaded documents when knowledge groups are selected
- **LLM general knowledge preserved** — when no knowledge group is selected, the assistant draws on the LLM's training knowledge directly
- **Asynchronous AI calls** — LLM latency is handled gracefully via a queue-and-poll pattern
- **Reference, not platform** — system prompts are not heavily optimised; data structures are intentionally generalised

---

## 3. What You Can Do With It

### End Users

| Capability | Description |
|---|---|
| Multi-turn chat | Contextual conversation maintained across messages |
| Model selection | Choose between available LLM models (Claude variants via AWS Bedrock) |
| Query LLM knowledge | Ask general questions answered from the model's training data |
| Query personal documents | Upload files and ask questions grounded in their content |
| Personal knowledge groups | Organise uploaded documents into named groups per prompt |
| Upload documents | PDF, DOCX, PPTX, and JSONL supported |
| Submit feedback | Rate and comment on assistant responses |

### Administrators / Document Owners

| Capability | Description |
|---|---|
| Create knowledge groups | Name and describe a group to associate uploaded documents |
| Upload documents | Manually upload files to a knowledge group for indexing |
| Track ingest status | Monitor whether uploaded documents are ready to query |

### Developers

| Capability | Description |
|---|---|
| Run full stack locally | Docker Compose with Bedrock Stub — no AWS credentials required |
| Run unit tests | pytest (Agent, Knowledge), Vitest (Frontend) |
| Run journey tests | WebdriverIO E2E and accessibility tests |
| Run performance tests | JMeter load tests with pre-seeded data |
| Inspect API docs | Swagger UI on Agent (`/docs`) and Knowledge services |

### DevOps / Platform Teams

| Capability | Description |
|---|---|
| Deploy to CDP | All services containerised; follow CDP CI/CD patterns |
| Observe metrics | AWS CloudWatch embedded metrics |
| Security scanning | Trivy vulnerability scanning in all CI/CD pipelines |
| Code quality | SonarCloud analysis on all services |

---

## 4. Limitations

### Scope Boundaries (by Design)

- **Reference implementation only** — system prompts not optimised; data models are generalised and need adaptation for production
- **Manual upload only** — no SharePoint, shared drives, or CRUD over external file stores
- **Personal knowledge groups only** — shared groups are not implemented
- **RAG on first turn only** — one RAG lookup per conversation turn, not iteratively refined

### Query Capabilities Not Delivered

- **Hybrid search** — keyword + vector search combined is not implemented; nearest-neighbour vector only
- **Whole document context** — questions requiring synthesis across multiple sections are unlikely to be answered well
- **Streaming responses** — LLM response returned in full once complete; no token-by-token streaming

### Document Types Not Supported

CSV, XLSX, images, audio/voice, legacy formats (`.doc`, `.xls`, `.ppt`, `.rtf`), and open/Google document formats.

### Known WIP Areas

The Knowledge service README notes the repository is **work in progress** — some API documentation may not reflect the current implementation.

---

## 5. Ideas for Future Work

### Query Quality
- Hybrid search (vector + BM25 keyword)
- Re-ranking after vector retrieval
- Query expansion before embedding
- Metadata filtering on vector search

### Document Handling
- Shared knowledge groups
- External source integration (SharePoint, shared drives)
- Additional file types (CSV, XLSX, images)
- Whole-document context strategies (map-reduce, sliding window)

### User Experience
- Streaming responses
- Source citations linking back to original document
- Conversation naming and history management
- Feedback loop driving prompt improvement

### Technical Maturity
- Domain-specific prompt optimisation
- Extended observability (latency percentiles, RAG hit/miss rates)
- LLM-as-judge automated evaluation in test suite
- Chunking strategy experimentation per document type
