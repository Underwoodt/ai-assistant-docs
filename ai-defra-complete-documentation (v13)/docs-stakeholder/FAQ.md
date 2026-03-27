# FAQ

[← Back to Stakeholder Docs](README.md)



---

## General

**What is this system?**
An AI chat assistant that answers questions from its general knowledge or from documents you upload. Built by DEFRA as a reference that other teams can use as a starting point for their own AI assistants.

**Is this a platform I can use for my project?**
It is a reference implementation — a proven starting point with working code, not a shared service you plug into. Teams clone, adapt, and deploy their own version tailored to their domain.

**Can I ask it anything?**
Yes, with nuance. Without a knowledge group selected, the AI answers from its general training knowledge. With a knowledge group selected, it searches your documents and grounds its answer in them. It cannot access the internet or live data in either mode.

**Does it work without documents?**
Yes. Without a knowledge group, the assistant answers entirely from the AI model's general knowledge.

---

## Documents

**What file types can I upload?**
PDF, DOCX, PPTX, and JSONL. CSV, XLSX, images, audio, and legacy formats (`.doc`, `.xls`, `.ppt`) are not supported.

**How long until a document is ready after upload?**
Typically seconds to a few minutes depending on file size. Status changes from "processing" to "ready" in the knowledge group view.

**Why did my document fail to ingest?**
Most common causes: unsupported format, corrupted or password-protected file, or a temporary processing error. Try re-uploading. If the problem persists, contact the team managing your deployment.

**Can the AI read the whole document?**
The AI retrieves the most relevant sections rather than reading the full file. Works well for specific factual questions; less well for full-document summaries or cross-document synthesis.

**Are my documents private?**
Yes. Only you can see and query your knowledge groups.

---

## AI Responses

**How does the AI decide what to answer?**
With a knowledge group selected: embeds your question as a mathematical vector, finds the most similar document sections, and sends those sections plus your question to the AI model. Without a knowledge group: your question goes directly to the AI model.

**Can I trust the answers?**
Treat responses as a starting point, not a definitive answer. The AI can make mistakes or confidently produce incorrect information. Always verify against source documents for anything important.

**Why is the AI sometimes slow?**
AI model inference takes several seconds for a full response. The loading indicator is normal, not an error.

**Does the AI learn from my questions or documents?**
No. The AI model is not updated based on your interactions. Content is used only to generate responses in your session.

---

## Access & Security

**How do I sign in?**
With your normal DEFRA Microsoft account. No separate account or password needed.

**Who can see my conversations?**
Only you.

**Is my data stored securely?**
Yes. All data is within DEFRA's approved AWS environment, encrypted in transit and at rest. See [Security & Privacy](SECURITY-PRIVACY.md) for full detail.
