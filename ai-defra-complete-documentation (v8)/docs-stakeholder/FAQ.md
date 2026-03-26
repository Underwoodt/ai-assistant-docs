# Frequently Asked Questions

---

**Can I ask it anything?**

Yes. On the first turn of a conversation, the tool searches your selected knowledge group files for relevant passages and uses those to ground its answer. It can also draw on the AI model's general training knowledge (up to its training date), so questions not covered by your documents can still receive a useful response. Bear in mind that answers based on general AI knowledge — rather than your uploaded documents — will not reflect DEFRA-specific content or policy.

---

**Why is the answer sometimes short or incomplete?**

The tool retrieves and uses passages from your documents, not whole documents. Questions that require synthesising an entire document — for example, "summarise this 80-page report" — are not well suited to this tool in its current form. The answer will reflect whatever relevant passages it found, which may not be comprehensive.

---

**Can my colleagues see my documents or conversations?**

No. Each user's uploaded documents and conversations are personal. There is no shared access between users.

---

**What file types can I upload?**

PDF, Word (.docx), PowerPoint (.pptx), and JSONL files. Other formats — including Excel, images, CSV, and older Office formats — are not currently supported.

---

**Is this the same as asking ChatGPT or Copilot?**

No. General AI tools like ChatGPT answer from their training data — which may be outdated, inaccurate, or not specific to DEFRA policy. This tool answers only from documents you have uploaded, and every answer is grounded in that content. This makes it safer for policy and operational use where accuracy matters.

---

**Can I connect it to SharePoint or a shared drive?**

Not currently. Documents must be uploaded manually. Automated connection to shared document stores is on the roadmap for future development.

---

**How does the tool decide which parts of my documents to use?**

It converts your question into a mathematical representation of its meaning, then finds the document passages whose meanings are closest to your question. Only passages above a relevance threshold are used — loosely related content is filtered out automatically.

---

**Is my data used to train AI models?**

No. Your documents and conversations are used only to answer your queries. They are not sent to any external service for training purposes and do not leave DEFRA's infrastructure.

---

**Can other DEFRA teams use this system?**

The system is designed as a reference implementation — a starting point, not a shared platform. Teams with their own document sets and requirements should fork the codebase and configure it for their context. Running it as a shared multi-team service would require additional work beyond what has been built here.

---

**Who do I contact if I have a problem?**

Raise an issue on the relevant GitHub repository or contact the DEFRA development team responsible for this project.
