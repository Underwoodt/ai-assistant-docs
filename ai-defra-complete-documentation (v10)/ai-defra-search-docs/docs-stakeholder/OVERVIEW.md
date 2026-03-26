# AI DEFRA Search — Overview

[← Back to Stakeholder Docs](README.md)

> **For:** Stakeholders, policy leads, programme managers
> **Licence:** Open Government Licence v3

---

## What Is It?

AI DEFRA Search is a chat tool that lets DEFRA staff ask questions of their own documents and get clear, accurate answers — without needing to read through the documents manually.

Rather than typing keywords into a search box and sifting through results, a user simply asks a question in plain English. The tool finds the most relevant passages in the uploaded documents and uses an AI assistant to compose a clear, readable answer based directly on that content.

---

## Why Was It Built?

DEFRA staff regularly need to extract specific information from large collections of policy documents, guidance notes, and reports. Traditional keyword search is slow, and general-purpose AI tools risk generating plausible-sounding answers that are not grounded in actual DEFRA policy.

This tool solves both problems: it is fast, and every answer is tied to real source documents — not the AI's general training.

The project also serves as a **reference implementation** for DEFRA. Other teams who want to build similar document-querying tools can use this codebase as a proven, production-ready starting point rather than building from scratch.

---

## Who Is It For?

| Audience | How they use it |
|---|---|
| **DEFRA policy and operational staff** | Ask questions of uploaded documents; get grounded answers quickly |
| **DEFRA development teams** | Fork and adapt the codebase for their own document sets and use cases |
| **Programme and delivery managers** | Understand the tool's scope, limitations, and planned future development |

---

## Key Principles

**Grounded answers only.** The AI does not draw on its general training to answer questions. It only synthesises content from documents you have uploaded. If the answer is not in the documents, the tool will say so.

**Personal and controlled.** Each user works with their own set of uploaded documents. There is no shared pool that other users can access without your involvement.

**Built to be adapted.** This is not a finished product for everyone to plug into. It is a high-quality starting point — teams with different document sets and requirements should fork and configure it for their context.

For a full description of capabilities and limitations, see `HIGH_LEVEL_OVERVIEW.md`.
