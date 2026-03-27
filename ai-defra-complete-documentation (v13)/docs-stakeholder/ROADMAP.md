# Considerations for Adopting Teams

[← Back to Stakeholder Docs](README.md)



---

This document outlines areas that teams should consider and plan for when adopting this reference implementation. These are not features built into this project — they are investment areas for any team taking it forward into a mature, production-ready service.

---

## Data & Access

| Consideration | What it means for your team |
|---|---|
| **Shared knowledge groups** | If documents should be shared across users (e.g. a team-wide policy library), access control and group ownership design is needed. Personal-only today. |
| **External document sources** | If your documents live in SharePoint, shared drives, or other systems, an ingestion integration is required. Currently manual upload only. |
| **Document lifecycle** | Plan how outdated documents will be removed or updated. No automated expiry or versioning today. |
| **Supported file types** | CSV, XLSX, images, audio, and legacy formats are not handled. Extending the ingestion pipeline is required. |

---

## Query Capabilities

| Consideration | What it means for your team |
|---|---|
| **Question complexity** | Current RAG is optimised for questions answered within small sections of text. Whole-document synthesis requires extending the retrieval strategy. |
| **Search quality** | Basic nearest-neighbour vector search only. Keyword search, re-ranking, or metadata filtering may improve accuracy for your domain. |
| **Prompt optimisation** | System prompts are not tuned for any specific domain. Investment in prompt engineering for your subject matter improves answer quality. |
| **RAG evaluation** | No automated evaluation of answer quality today. Establish a way to measure correctness before operational reliance. |

---

## User Experience

| Consideration | What it means for your team |
|---|---|
| **Response time** | Responses appear in full after generation completes — no streaming. May feel slow for longer answers. |
| **Source transparency** | Users are not shown which document sections were used to generate an answer. Adding citations improves trust. |
| **Conversation management** | Users cannot name, save, or organise past conversations. |
| **Accessibility** | Journey tests include WCAG 2.1 checks. Run these against any customised version and fix findings before go-live. |

---

## Technical Maturity

| Consideration | What it means for your team |
|---|---|
| **Data schema adaptation** | The data models are generalised. Review whether the fields meet your domain's needs before production. |
| **Observability** | Basic CloudWatch metrics are included. Extend with latency percentiles, error rates, and RAG-specific metrics for production. |
| **Scaling** | Review the queueing and ingestion design if expecting high volumes of concurrent uploads or queries. |
| **Security review** | Conduct a DPIA and verify configuration meets your data classification requirements before deployment. |
