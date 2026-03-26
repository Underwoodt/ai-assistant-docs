# Frequently Asked Questions

[← Back to Stakeholder Docs](README.md)

---

## General

**What is this system?**
An AI chat assistant that can answer questions from its general AI knowledge or from specific documents you upload. It was built by DEFRA as a reference that other teams can use as a starting point for their own AI assistants.

**Is this a platform I can use for my project?**
It is a reference implementation — a proven starting point with working code, not a shared service you plug into. Teams are expected to clone, adapt, and deploy their own version tailored to their domain, data requirements, and users.

**Can I ask it anything?**
Yes, with some important nuances. If you have not selected a knowledge group, the AI answers from its general training knowledge (up to its knowledge cutoff date). If you have selected a knowledge group, it searches your uploaded documents for relevant content and grounds its answer in those documents. It cannot access the internet or live data in either mode.

**Does it work without documents?**
Yes. If you do not select a knowledge group, the assistant answers entirely from the AI model's general knowledge — much like a standard AI chatbot.

---

## Documents

**What file types can I upload?**
PDF, DOCX, PPTX, and JSONL are supported. CSV, XLSX, images, audio files, and legacy formats (`.doc`, `.xls`, `.ppt`) are not currently supported.

**How long does it take for a document to be ready after upload?**
Typically seconds to a few minutes, depending on file size. The system processes the document in the background — you can see the status change from "processing" to "ready" in the knowledge group view.

**Why did my document fail to ingest?**
The most common causes are: an unsupported file format, a corrupted or password-protected file, or a temporary processing error. Try re-uploading. If the problem persists, contact the team maintaining your deployment.

**Can the AI read the whole document?**
The AI retrieves the most relevant sections of your documents rather than reading the entire file in full. This works well for specific factual questions but less well for questions requiring a full-document summary or cross-document synthesis.

**Are my documents private?**
Yes. Your uploaded documents are only accessible to you — other users cannot see or query your knowledge groups.

---

## AI Responses

**How does the AI decide what to answer?**
If you have selected a knowledge group, the system searches your documents for sections most closely matching your question (using AI-generated similarity scores), then gives those sections to the AI model as context alongside your question. If no knowledge group is selected, your question goes directly to the AI model.

**Can I trust the AI's answers?**
Treat responses as a starting point rather than a definitive answer. The AI can make mistakes, misinterpret questions, or confidently produce incorrect information. For document-grounded answers, always verify against the original source document for anything important.

**Why is the AI slow sometimes?**
AI model inference takes time — typically several seconds for a full response. The system shows a loading indicator while the response is being generated. This is normal behaviour and not an error.

**Does the AI learn from my questions or documents?**
No. The AI model is not updated or trained based on your interactions. Your documents and conversations are used only to generate responses in your session.

---

## Access & Security

**How do I sign in?**
Use your normal DEFRA Microsoft account. No separate account or password is needed.

**Who can see my conversations?**
Only you. Conversations are linked to your user account and not accessible to other users.

**Is my data stored securely?**
Yes. All data is stored within DEFRA's approved AWS environment, encrypted in transit and at rest, and access-controlled by API keys and IAM roles. See [SECURITY-PRIVACY.md](SECURITY-PRIVACY.md) for full detail.
