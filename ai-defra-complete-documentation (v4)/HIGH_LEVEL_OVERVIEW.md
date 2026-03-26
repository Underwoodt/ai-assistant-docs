# AI DEFRA Search — High-Level Overview

> **Project:** AI DEFRA Search RAG Chat Assistant
> **Owner:** Department for Environment, Food & Rural Affairs (DEFRA)
> **Licence:** Open Government Licence v3

---

## 1. What We Built

AI DEFRA Search is a browser-based chat assistant that lets users ask questions of a curated set of documents using AI. Rather than searching for keywords, users have a natural-language conversation with the system, which finds the most relevant passages from uploaded documents and uses a large language model (LLM) to compose a clear, grounded answer.

The system is made up of four core services working together:

| Service | Plain-English Role |
|---|---|
| **Frontend** | The browser interface users interact with — chat window, file uploads, model selection |
| **Agent** | The brain — receives questions, retrieves relevant document passages, sends them to the AI model, and manages the conversation |
| **Knowledge** | The library — stores uploaded documents, breaks them into searchable chunks, and finds the most relevant ones when asked |
| **Bedrock Stub** | A local stand-in for AWS AI services, so developers can work without a real cloud account |

Full architecture detail is in `docs-developer/ARCHITECTURE.md`.

---

## 2. Why We Built It

DEFRA needed a way for staff to query large collections of policy documents, guidance notes, and reports without the overhead of manual search or the risk of AI models making things up. The system is designed around two core principles:

**Grounded answers, not hallucinations.** Every response is anchored to specific document chunks retrieved from the knowledge store. The AI does not draw on general training knowledge for answers — it only synthesises what is in the uploaded documents.

**A paved road, not a finished product.** This project is explicitly a reference implementation — a working, production-quality example that other DEFRA teams can fork and adapt for their own document sets and use cases. It is not intended to be a shared platform that every team plugs into directly, because different teams have different data, different questions, and different access requirements.

The primary users are DEFRA staff who need to query internal documents. Developers across DEFRA can use this codebase as a starting point for their own RAG implementations.

---

## 3. What You Can Do With It

### End Users

| Capability | Detail |
|---|---|
| Chat with documents | Ask questions in natural language; receive answers grounded in uploaded content |
| Select your AI model | Choose from a list of available LLMs (e.g. different Claude versions) |
| Manage personal knowledge groups | Upload your own files (PDF, DOCX, PPTX, JSONL) and group them for targeted queries |
| Multi-turn conversations | Follow-up questions work — the system maintains conversation context |
| Submit feedback | Rate and comment on responses to help improve the system |

### Developers

| Capability | Detail |
|---|---|
| Run the full stack locally | All services run via Docker Compose — no real AWS account needed (Bedrock stub provided) |
| Hot-reload development | Code changes in any service are reflected immediately without rebuilding |
| Run automated tests | Unit and integration tests across all services; 90%+ coverage enforced |
| Load demo data | Script to populate knowledge groups with sample content for testing |

### DevOps / Platform Teams

| Capability | Detail |
|---|---|
| Deploy to CDP (DEFRA platform) | All services are containerised and follow DEFRA's Common Deployment Platform patterns |
| Switch between stub and real Bedrock | Two Docker Compose variants: one using the stub, one connecting to AWS Bedrock |
| Monitor with CloudWatch | Embedded metrics sent to AWS CloudWatch from both Agent and Knowledge services |

---

## 4. Limitations

The following limitations are important to understand before adopting or extending this system:

**RAG chunk-level answers only.** The system retrieves and uses chunks (passages) of documents, not whole documents. Questions that require synthesising an entire document — for example, "summarise this 50-page report" — are unlikely to be answered well. This is a deliberate architectural decision, not a bug.

**Manual document upload only.** There is no automated ingestion pipeline from SharePoint, shared drives, or other document stores. Documents must be uploaded manually through the interface. Bulk ingestion via JSONL is supported for developer workflows.

**Personal knowledge groups only.** There is no shared knowledge group feature — each user's uploaded documents are their own. Teams cannot share a common document pool through the UI.

**No hybrid search.** The system uses semantic (vector) search only. Keyword-based search (which can be more reliable for exact terms, codes, or references) is not implemented.

**Document types supported:** PDF, DOCX, PPTX, JSONL. Not supported: CSV, XLSX, images, voice, legacy formats (DOC, XLS, PPT), Google/OpenOffice formats.

**Synchronous responses only.** Chat responses are not streamed — users see nothing until the full answer is ready. For complex questions this may mean a noticeable wait.

**Reference implementation, not a shared platform.** This codebase is designed to be forked and adapted. Running it as a shared multi-team service is out of scope and would require significant additional work around data isolation, access control, and governance.

---

## 5. Ideas for Future Work

The following improvements have been identified as natural next steps. Items marked 🗺 are likely to be needed by any team adopting this codebase.

| Area | Description | Notes |
|---|---|---|
| 🗺 Shared knowledge groups | Allow teams to share a common document pool | Currently out of scope; each team will need to design this for their context |
| 🗺 Hybrid search | Combine vector and keyword search for more reliable retrieval | Particularly useful for exact references, codes, and proper nouns |
| 🗺 Whole-document context | Support summarisation and cross-document synthesis | Requires different retrieval strategy (e.g. document-level context window) |
| Streaming responses | Stream LLM output token-by-token for a more responsive feel | Frontend and Agent changes required |
| Metadata handling | Tag documents with author, date, topic for filtered retrieval | Currently not stored or used in retrieval |
| SharePoint / shared drive integration | Automated ingestion from document stores | CRUD over file stores not currently implemented |
| Additional document types | CSV, XLSX, images, legacy Office formats | Extractors would need to be written per type |
| Accessibility audit | Formal WCAG 2.1 AA review of the frontend | Journey tests include some accessibility checks; formal audit not yet done |
| Expanded test coverage | Performance and load test results baseline | JMeter framework exists; baseline benchmarks not yet established |
