# AI DEFRA Search — High-Level Overview

> **Project:** AI DEFRA Search (AI Assistant)
> **Platform:** Core Delivery Platform (CDP)
> **Status:** Reference implementation / production proof-of-concept

---

## 1. What We Built

AI DEFRA Search is an end-to-end **Retrieval-Augmented Generation (RAG) chat assistant** built as a reference implementation on DEFRA's Core Delivery Platform. It demonstrates how AI-powered search and conversational interfaces can be deployed in a secure, government-grade environment using AWS Bedrock as the AI inference layer.

The system is composed of three core services and a supporting test and tooling layer:

| Service | Technology | Role |
|---|---|---|
| **Frontend** | Node.js, Hapi, Nunjucks | Browser-based chat UI — model selection, file upload, conversation display |
| **Agent Service** | Python, FastAPI | Chat API brain — multi-turn conversation state, LLM routing via AWS Bedrock, RAG orchestration |
| **Knowledge Service** | Python, FastAPI, pgvector | Document ingestion pipeline — chunking, embedding generation, vector storage, semantic search |
| **CDP Uploader** | CDP platform service | Secure file upload to S3 with virus scanning and callback notification |
| **AWS Bedrock** | AWS managed service | LLM inference (Claude models) and embedding generation (Titan Embed v2) |
| **AWS SQS** | AWS managed service | Async message queue decoupling the frontend from the Agent's LLM processing |

Supporting repos:

| Repo | Purpose |
|---|---|
| `ai-defra-search-journey-tests` | End-to-end and accessibility tests (WebdriverIO) |
| `ai-defra-search-perf-tests` | Load and performance tests (Apache JMeter) |
| `ai-defra-search-aws-bedrock-stub` | WireMock stub enabling fully offline local development |

---

## 2. Why We Built It

### The Problem

DEFRA teams wanted to enable staff and policy users to query their own document collections using natural language — without waiting for a central AI platform team to build something for them. No approved, reusable RAG pattern existed on CDP.

### The Approach

Rather than building a finished product, the team built a **paved road**: a working, production-deployed reference that other CDP teams can fork, adapt to their own data, and extend with their own retrieval strategies. The design principles were:

- **Generalisable first** — data structures and system prompts are intentionally generic so they can be adapted, not prescribed
- **CDP-native** — the upload, auth, and deployment patterns all follow CDP conventions so adopters get compliant infrastructure from day one
- **Extensible RAG baseline** — the RAG pipeline uses a clean nearest-neighbour lookup with no re-ranking or query expansion, so teams can layer on metadata filtering, hybrid search, or rerankers without architectural surgery
- **Offline-capable** — the Bedrock stub means developers never need AWS credentials to run the full stack locally

### Users

The primary users are **DEFRA staff** who want to ask questions against a curated set of documents they have personally uploaded. Secondary users are **developer teams** at DEFRA who want a working reference to understand how to build their own RAG assistant on CDP.

---

## 3. What You Can Do With It

### As an End User

| Capability | Detail |
|---|---|
| **Chat with an LLM** | Select from a curated list of available Claude models and ask questions in natural language |
| **Query your own documents** | Create a personal Knowledge Group, upload files, and target that group when asking questions |
| **Upload documents** | Supported formats: PDF, DOCX, PPTX, JSONL |
| **Asynchronous responses** | LLM responses are non-blocking — a loading state is shown while Bedrock processes the query |
| **View conversation history** | Multi-turn conversation state is persisted in MongoDB and surfaced in the UI |

### As a Developing Team (Adopter)

| Capability | Detail |
|---|---|
| **Fork and adapt** | All three services are independently deployable; swap in your own document corpus and system prompt |
| **Extend the RAG pipeline** | Clean service boundary between Agent and Knowledge Service makes it straightforward to add hybrid search, reranking, or metadata filtering |
| **Run fully offline** | AWS Bedrock stub (WireMock) simulates Claude and Titan Embed APIs locally — no AWS account needed |
| **Test coverage included** | Journey tests (WebdriverIO) and performance tests (JMeter) ship with the project |

---

## 4. Limitations

The following are intentional design constraints or known gaps as of this reference implementation:

| Area | Limitation |
|---|---|
| **Knowledge Groups** | Personal only — no shared groups, no link to SharePoint or shared drives |
| **Upload method** | Manual upload only — no automated ingestion from file stores |
| **Document types** | CSV, XLSX, images, voice, legacy Office formats (`.doc`, `.xls`, `.ppt`), RTF, and Google/OpenOffice formats are **not** supported |
| **RAG retrieval** | Nearest-neighbour only — no hybrid keyword+vector search, no re-ranking, no query expansion |
| **Query scope** | Questions that require understanding across many chunks or whole-document context (e.g. full document summarisation, cross-document synthesis) are unlikely to be answered well |
| **Metadata handling** | No CRUD over file stores; metadata fields are stored but not used for filtering |
| **System prompts** | Not extensively optimised — teams should tune prompts for their specific use case |
| **Shared knowledge** | Teams needing shared, organisation-wide knowledge bases will need to extend the Knowledge Service |

---

## 5. Ideas for Future Work

The following improvements have been identified as natural next steps for teams adopting or extending this reference:

| Area | Idea |
|---|---|
| **Hybrid search** | Add BM25 or keyword-based retrieval alongside vector search for better recall on exact-match queries |
| **Re-ranking** | Add a cross-encoder re-ranking step after retrieval to improve answer quality |
| **Whole-document context** | Support sending full documents to the LLM for summarisation tasks (where token budgets allow) |
| **Shared Knowledge Groups** | Allow teams to share knowledge groups with other users or link to SharePoint / shared drives |
| **Broader document support** | Add extractors for CSV, XLSX, images (via OCR), voice (via transcription), and legacy Office formats |
| **Metadata filtering** | Use document metadata (owner, date, type) to narrow retrieval scope before vector search |
| **Streaming responses** | Stream LLM output token-by-token to improve perceived latency for users |
| **Automated ingestion** | Trigger ingestion from S3 events or SharePoint webhooks rather than manual upload only |
| **Multi-model routing** | Route queries to different models based on task type (e.g. cheaper model for classification, larger model for generation) |
| **Evaluation framework** | Add RAG evaluation tooling (e.g. RAGAS) to measure retrieval precision and answer faithfulness |
