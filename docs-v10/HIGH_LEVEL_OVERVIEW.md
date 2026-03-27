# AI DEFRA Search — High-Level Overview

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
| **Bedrock Stub** | WireMock | Offline stand-in for AWS Bedrock Claude and Titan Embed APIs — no cloud account needed for local dev |
| **Journey Tests** | WebdriverIO / Node.js | End-to-end and WCAG accessibility tests covering the main user flows |
| **Performance Tests** | Apache JMeter | Load, stress, and throughput tests against the full stack |

For a detailed technical breakdown of how these components interact, see [docs-developer/ARCHITECTURE.md](docs-developer/ARCHITECTURE.md).

---

## 2. Why We Built It

### The Problem

DEFRA teams looking to add AI-powered search to their services had no internal reference to draw on. Deploying a RAG assistant involves a non-trivial combination of cloud AI APIs, vector databases, asynchronous processing, document ingestion pipelines, and authentication — each with their own CDP-specific considerations. Without a reference, every team would have to design and build this independently.

### The Goal

To provide a practical, end-to-end paved road for any DEFRA team that wants to deploy a similar AI assistant on CDP. The project demonstrates that this is achievable within DEFRA's existing platform and tooling, and reduces the cost for teams to follow the same pattern.

The project is explicitly scoped as a **reference implementation** — it shows one way to do this well, not the only way. Teams are expected to adapt the data models, system prompts, and retrieval strategies to their own use cases.

### Key Design Principles

- **CDP-first** — runs entirely on DEFRA's Core Delivery Platform; follows CDP conventions for deployment, CI/CD, and secrets management
- **Offline development** — the Bedrock Stub means developers can run the full stack locally without AWS credentials
- **RAG grounded by default** — user queries are answered from uploaded documents when knowledge groups are selected, reducing hallucination
- **LLM general knowledge preserved** — when no knowledge group is selected, the assistant draws on the LLM's training knowledge directly
- **Asynchronous AI calls** — LLM latency is handled gracefully via a queue-and-poll pattern rather than blocking the user
- **Reference, not platform** — the system prompts have not been heavily optimised; data structures are intentionally generalised

---

## 3. What You Can Do With It

### End Users

| Capability | Description |
|---|---|
| Multi-turn chat | Have a contextual conversation with the assistant across multiple turns |
| Model selection | Choose between available LLM models (Claude variants via AWS Bedrock) |
| Query LLM knowledge | Ask general questions answered from the model's training data |
| Query personal documents | Upload files and ask questions grounded in their content |
| Personal knowledge groups | Organise uploaded documents into named groups per prompt |
| Upload documents | PDF, DOCX, PPTX, and JSONL files are supported |
| Submit feedback | Rate and comment on assistant responses |

### Administrators / Document Owners

| Capability | Description |
|---|---|
| Create knowledge groups | Name and describe a group to associate uploaded documents |
| Upload documents | Manually upload files to a knowledge group for indexing |
| Track ingest status | Monitor whether uploaded documents have been processed and are ready to query |

### Developers

| Capability | Description |
|---|---|
| Run full stack locally | Docker Compose with the Bedrock Stub — no AWS credentials required |
| Run unit tests | pytest (Agent, Knowledge), Vitest (Frontend) |
| Run journey tests | WebdriverIO E2E and accessibility tests against local or CDP environment |
| Run performance tests | JMeter load tests with pre-seeded data |
| Debug remotely | Remote debugger support (port 5679 on Agent) |
| Inspect API docs | Swagger UI available on Agent (`/docs`) and Knowledge services |

### DevOps / Platform Teams

| Capability | Description |
|---|---|
| Deploy to CDP | All services containerised and follow CDP CI/CD patterns |
| Observe metrics | AWS CloudWatch embedded metrics on Agent and Knowledge services |
| Security scanning | Trivy vulnerability scanning integrated into all CI/CD pipelines |
| Code quality | SonarCloud analysis on all services |

---

## 4. Limitations

### Scope Boundaries (by Design)

- **Reference implementation only** — system prompts are not optimised; data models are generalised and will need adaptation for production use cases
- **Manual upload only** — there is no integration with SharePoint, shared drives, or any CRUD over external file stores
- **Personal knowledge groups only** — shared knowledge groups (accessible to multiple users) are not implemented
- **RAG on first turn only** — the RAG lookup is performed once per conversation turn, not iteratively refined

### Query Capabilities Not Delivered

- **Hybrid search** — keyword + vector search combined is not implemented; nearest-neighbour vector search only with no re-ranking or query expansion
- **Whole document context** — questions requiring synthesis across multiple sections or whole documents (summaries, cross-document comparisons) are unlikely to be answered well; the system is optimised for chunk-level retrieval
- **Streaming responses** — the LLM response is returned in full once complete; there is no token-by-token streaming to the user

### Document Types Not Supported

CSV, XLSX, images, audio/voice, legacy formats (`.doc`, `.xls`, `.ppt`, `.rtf`), and open/Google document formats are not handled.

### Infrastructure Constraints

- **AWS Bedrock dependency** — LLM inference and embeddings require AWS Bedrock (or the local stub). There is no offline fallback for production.
- **CDP deployment target** — the CI/CD and deployment configuration is tailored to CDP; significant rework would be needed to deploy elsewhere.

### Known WIP Areas

- The Knowledge service README notes the repository is **work in progress**; some API documentation may not reflect the current implementation.

---

## 5. Ideas for Future Work

The following improvements have been identified based on the project goals, known limitations, and common patterns for maturing a RAG system. Items are framed as considerations for teams adopting this reference.

### Query Quality

- **Hybrid search** — combine vector similarity with keyword (BM25) search for better recall on exact-match queries
- **Re-ranking** — add a re-ranker step after vector retrieval to improve result relevance before sending to the LLM
- **Query expansion** — rephrase or expand the user's query before embedding to improve retrieval coverage
- **Metadata filtering** — filter the vector search by document metadata (date, type, source) before similarity scoring

### Document Handling

- **Shared knowledge groups** — allow teams to share curated knowledge groups across users
- **External source integration** — connect to SharePoint, shared drives, or other file stores for automatic ingestion
- **Additional document types** — extend the ingestion pipeline to handle CSV, XLSX, images, and legacy formats
- **Whole document context** — explore LLM context window strategies (map-reduce, sliding window) for summarisation and cross-document synthesis

### User Experience

- **Streaming responses** — stream LLM tokens to the browser as they arrive for a more responsive feel
- **Source citations** — surface which document chunks contributed to each answer with links back to the source
- **Conversation management** — allow users to name, save, and revisit past conversations
- **Feedback loop** — use collected user feedback to identify poor responses and inform prompt tuning

### Technical Maturity

- **Prompt optimisation** — invest in systematic prompt engineering and evaluation for domain-specific use cases
- **Observability** — extend CloudWatch metrics with latency percentiles, RAG hit/miss rates, and embedding quality tracking
- **Automated evaluation** — add LLM-as-judge evaluation to the test suite to catch prompt regressions
- **Chunking strategies** — experiment with semantic chunking, larger/smaller chunk sizes, and overlap settings per document type
