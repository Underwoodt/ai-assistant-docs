# Features

[← Back to Stakeholder Docs](README.md)



---

## Delivered Capabilities

### For End Users

| Feature | Description |
|---|---|
| **AI Chat** | Multi-turn conversation; context maintained across messages in the same session |
| **General knowledge queries** | Ask the AI anything within its training knowledge |
| **Document-grounded queries** | Ask questions about specific uploaded documents; AI retrieves relevant sections |
| **Model selection** | Choose from available AI models for each conversation |
| **User feedback** | Rate responses; optionally add a comment |

### For Document Owners / Administrators

| Feature | Description |
|---|---|
| **Personal knowledge groups** | Create named groups to organise documents |
| **Document upload** | Upload files manually and associate with a knowledge group |
| **Supported file types** | PDF, DOCX, PPTX, JSONL |
| **Ingest status tracking** | See when a document has been processed and is ready to query |

---

## Where Document-Grounded Answers Work Well

The system retrieves small, relevant sections of documents by semantic similarity. This works well for specific factual questions answered within a single document section (e.g. "What is the threshold for X in the 2023 guidance?"). It works less well for questions requiring synthesis across entire documents or multiple files.

---

## What Is Not Included

| Capability | Status |
|---|---|
| Shared knowledge groups | Not delivered |
| Auto-ingestion from SharePoint / shared drives | Not delivered |
| Hybrid keyword + semantic search | Not delivered |
| Response streaming (token by token) | Not delivered |
| CSV, XLSX, image, audio support | Not delivered |
| Legacy format support (`.doc`, `.xls`, `.ppt`) | Not delivered |

See [Considerations for Adopting Teams](ROADMAP.md) for what teams would need to build out for these.
