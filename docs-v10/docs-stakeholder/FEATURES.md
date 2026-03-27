# Features

[← Back to Stakeholder Docs](README.md)

---

## Delivered Capabilities

### For End Users

| Feature | Description |
|---|---|
| **AI Chat** | Multi-turn conversation with an AI assistant, maintaining context across messages in the same session |
| **General knowledge queries** | Ask the AI anything within its training knowledge — history, science, policy background, writing help |
| **Document-grounded queries** | Ask questions about specific uploaded documents; the AI retrieves relevant sections and grounds its answer in your content |
| **Model selection** | Choose from available AI models for each conversation |
| **User feedback** | Rate responses as helpful or unhelpful; optionally add a comment |

### For Document Owners / Administrators

| Feature | Description |
|---|---|
| **Personal knowledge groups** | Create named groups to organise your documents — e.g. "Policy Papers Q3" or "Project Background" |
| **Document upload** | Upload files manually and associate them with a knowledge group |
| **Supported file types** | PDF, DOCX, PPTX, JSONL |
| **Ingest status tracking** | See when a document has been processed and is ready to query |

---

## What the AI Can and Cannot Answer

Understanding where document-grounded answers work well helps set realistic expectations.

### Works well

- Specific factual questions answered within a single document section (e.g. "What is the threshold for X in the 2023 guidance?")
- Questions where the answer is contained in one or a small number of coherent chunks of text
- General knowledge questions not requiring document content

### Works less well

- Questions requiring the AI to synthesise information from across an entire document or multiple documents (e.g. "Summarise the key themes across all uploaded reports")
- Questions where the answer spans many separate sections that cannot be retrieved together
- Complex comparisons or cross-document analysis

This is a known characteristic of the current RAG (Retrieval-Augmented Generation) approach, which retrieves small chunks of text by similarity. Teams with use cases requiring whole-document understanding should consider this when evaluating adoption.

---

## What Is Not Included

| Capability | Status |
|---|---|
| Shared knowledge groups (shared across users) | Not delivered |
| Automatic ingestion from SharePoint / shared drives | Not delivered |
| Hybrid search (keyword + semantic) | Not delivered |
| Response streaming (token by token) | Not delivered |
| CSV, XLSX, image, audio file support | Not delivered |
| Legacy format support (`.doc`, `.xls`, `.ppt`) | Not delivered |

These are known future considerations — see [ROADMAP.md](ROADMAP.md).
