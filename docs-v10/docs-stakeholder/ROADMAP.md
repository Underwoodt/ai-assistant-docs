# Considerations for Adopting Teams

[← Back to Stakeholder Docs](README.md)

---

This document outlines the areas that teams should consider and plan for when adopting this reference implementation for their specific use case. These are not features built into this project — they are open questions and investment areas for any team taking it forward into a mature, production-ready service.

---

## Data & Access

| Consideration | What it means for your team |
|---|---|
| **Shared knowledge groups** | Decide whether documents should be shareable across users (e.g. a team-wide policy library). The current system is personal-only — shared access requires design work on access control and group ownership. |
| **External document sources** | If your team's documents live in SharePoint, shared drives, or other systems, you will need to build an integration to bring them in. Currently only manual upload is supported. |
| **Document lifecycle** | Plan how outdated documents will be removed or updated in knowledge groups. There is no automated expiry or versioning today. |
| **Supported file types** | If your team works with CSV, XLSX, images, audio, or legacy formats (`.doc`, `.xls`), these are not yet handled. Extending the ingestion pipeline would be required. |

---

## Query Capabilities

| Consideration | What it means for your team |
|---|---|
| **Question complexity** | The current RAG approach is optimised for questions answered within small sections of text. Questions requiring synthesis across a whole document or multiple documents may not perform well without extending the retrieval strategy. |
| **Search quality** | The system uses basic nearest-neighbour vector search. For higher accuracy, your team should consider adding keyword search, re-ranking, or metadata filtering based on your domain's content characteristics. |
| **Prompt optimisation** | The system prompts shipped in this reference are not tuned for any specific domain. Teams should invest in prompt engineering for their subject matter to improve answer quality. |
| **RAG evaluation** | There is no automated evaluation of answer quality today. Teams should establish a way to measure whether the AI is answering correctly before relying on it for operational decisions. |

---

## User Experience

| Consideration | What it means for your team |
|---|---|
| **Response time** | Responses appear in full after the AI finishes generating them (no streaming). For longer answers this may feel slow. Streaming responses would require frontend and API changes. |
| **Source transparency** | Users are not currently shown which parts of their documents were used to generate an answer. Adding source citations would improve trust and verifiability. |
| **Conversation management** | Users cannot name, save, or revisit past conversations in an organised way. Consider whether this matters for your users. |
| **Accessibility** | Journey tests include WCAG 2.1 accessibility checks. Teams should run these against any customised version and fix findings before going live. |

---

## Technical Maturity

| Consideration | What it means for your team |
|---|---|
| **Data schema adaptation** | The data models (knowledge groups, documents, conversations) are generalised. Review whether the fields and structure meet your domain's needs before going to production. |
| **Observability** | Basic CloudWatch metrics are included. Teams running this in production should extend monitoring with latency percentiles, error rates, and RAG-specific metrics (e.g. retrieval hit rate). |
| **Scaling** | The current architecture handles one document upload at a time per user. Teams expecting high volumes of concurrent uploads or queries should review the queueing and ingestion design. |
| **Security review** | Teams handling sensitive or personal data should conduct a DPIA and ensure the system configuration meets their data classification requirements before deployment. |
