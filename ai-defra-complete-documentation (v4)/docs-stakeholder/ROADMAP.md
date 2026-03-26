# Known Future Work & Roadmap

> Items below are drawn from the project goals document and known gaps in the current implementation. Items marked 🗺 are likely needed by any team adopting this codebase.

---

## Near-Term Improvements

| Area | Description |
|---|---|
| 🗺 **Shared knowledge groups** | Allow teams to create shared document pools accessible to multiple users. Currently each user manages their own documents only |
| 🗺 **Hybrid search** | Combine the current meaning-based (semantic) search with keyword matching. This would improve reliability for exact references, codes, and specific terminology |
| **Streaming responses** | Show the AI's answer as it is generated, word by word, rather than waiting for the full response. This would make the tool feel more responsive |

---

## Medium-Term Improvements

| Area | Description |
|---|---|
| 🗺 **Whole-document context** | Support questions that require reading a full document — e.g. "summarise this report". This is a more complex change requiring a different retrieval approach |
| **Metadata and filtering** | Tag documents with author, date, topic, and use those tags to filter search results — e.g. "only search documents from 2024" |
| **Additional file types** | Support for CSV, Excel, images, and legacy Office formats (older Word/PowerPoint/Excel) |
| **SharePoint / shared drive connection** | Automatically ingest documents from existing shared document stores rather than manual upload |

---

## Longer-Term Considerations

| Area | Description |
|---|---|
| **Formal accessibility audit** | A WCAG 2.1 AA review of the browser interface. Some accessibility testing is already in place; a formal audit has not yet been conducted |
| **Performance baseline** | The performance testing framework exists; formal benchmarks for response times and system capacity under load have not yet been established |
| **Platform reuse guide** | Documentation to help other DEFRA teams adapt and deploy this codebase for their own context |

---

> Items on this roadmap are identified needs, not committed delivery commitments. Prioritisation will depend on team capacity and the needs of adopting projects.
