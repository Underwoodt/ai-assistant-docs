# Product Overview

[← Back to Stakeholder Docs](README.md)



---

## What Is It?

The AI DEFRA Search Assistant is an AI-powered chat tool that lets users have conversations with an AI and ask questions about their own documents. It was built by DEFRA as a working reference that any team on DEFRA's Core Delivery Platform (CDP) can use as a starting point for building their own AI assistant.

Users can:
- Chat with an AI drawing on its general knowledge
- Upload their own documents and ask questions specifically about those documents
- Organise documents into knowledge groups, selecting which group to query per conversation

---

## Why Was It Built?

Several DEFRA teams were independently investigating how to add AI-powered document search to their services. This project was created to answer that question once, well — providing a fully working, deployable example rather than leaving each team to figure it out from scratch.

The system demonstrates that AI-powered search is achievable within DEFRA's existing platform, using approved cloud services and following CDP deployment standards.

It is a **reference implementation** — a proven starting point, not a finished product for general deployment. Teams adopting it are expected to tailor the AI behaviour, document handling, and user experience to their domain.

---

## Who Is It For?

**Document owners** who want to make their documents queryable — upload files, organise them into groups, then ask questions about content rather than searching manually.

**End users** who want to ask questions and receive AI-generated answers grounded in specific documents or from the AI's general knowledge.

---

## Where Does It Run?

The system runs on DEFRA's Core Delivery Platform (CDP) and uses Amazon Web Services for AI capabilities. It is accessed through a standard web browser — no software installation required.

---

## Relationship to Other Work

This is a reference implementation only — not a shared platform for all teams to reuse as-is. Each team's documents, user questions, and domain requirements will differ. The value is in the architecture and patterns demonstrated.

See [High-Level Overview](../HIGH_LEVEL_OVERVIEW.md) for the full project narrative including capabilities, limitations, and future work.
