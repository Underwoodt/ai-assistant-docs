# Features & Capabilities

---

## What Users Can Do

### Ask questions — documents and general knowledge

Users type a question in plain English. On the first turn of a conversation, the tool searches the user's selected knowledge group files for the most relevant passages and passes those to the AI to compose a grounded answer. The AI can also draw on its general training knowledge (up to its training date), so questions that aren't covered by uploaded documents can still receive a useful response — though those answers won't be grounded in DEFRA's specific content.

### Choose which AI model to use

The tool supports multiple AI language models. Users can select the model that best suits their needs from a dropdown — for example, choosing a model optimised for speed versus one optimised for depth.

### Manage personal document groups

Users can create named groups of documents and upload files into them. When asking a question, they can target a specific group — for example, asking only within "Q4 Policy Guidance" documents rather than across everything they have uploaded.

**Supported file types:** PDF, Word documents (.docx), PowerPoint files (.pptx), and structured data files (.jsonl).

### Hold multi-turn conversations

The tool remembers the context of a conversation. Users can ask follow-up questions without restating the full context — "What about the exceptions mentioned above?" will work as expected.

### Give feedback on responses

Users can rate and comment on answers. This feedback is stored and can be used to identify where the tool performs well or needs improvement.

---

## What the Tool Does Not Do

It is important to be clear about where the tool's limits are, so that teams do not rely on it for tasks it is not designed to handle.

| Not supported | Why |
|---|---|
| Summarising whole documents | The tool works with passages, not full documents. Summaries requiring full-document context will be incomplete |
| Cross-document synthesis | Merging and comparing sections across multiple documents is not currently supported |
| Shared document pools | Each user's documents are their own; team-shared libraries are not implemented |
| Keyword or reference search | The tool uses semantic (meaning-based) search only; exact phrase or code matching is not reliable |
| File types other than PDF, DOCX, PPTX, JSONL | CSV, Excel, images, voice, and older Office formats are not supported |
| Connecting to SharePoint or shared drives | Documents must be uploaded manually |

---

## Business Benefits

- **Time saving** — staff get direct answers rather than searching through long documents
- **Accuracy** — answers are tied to actual document content, reducing the risk of misremembering or misquoting policy
- **Reusability** — the codebase provides a production-quality starting point for other DEFRA AI document tools, avoiding teams duplicating effort
- **Offline-capable for developers** — the full system can run without internet access or a live AWS account, reducing development cost and risk
