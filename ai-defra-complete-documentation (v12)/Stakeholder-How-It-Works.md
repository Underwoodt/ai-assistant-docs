# How It Works

> 📊 **Stakeholder Docs** — [Home](Home) › [Stakeholder Docs](Home#stakeholder-docs)

---

## The Big Picture

The assistant is made up of three main parts working together: a website you use in your browser, an AI reasoning engine, and a document library.

```
Your Browser
    │
    ▼ HTTPS
┌──────────────┐    ┌──────────────────┐    ┌──────────────────────┐
│   Website    │───▶│   AI Engine      │───▶│   Document Library   │
│  (Frontend)  │    │  (Agent Service) │    │ (Knowledge Service)  │
└──────────────┘    └──────────────────┘    └──────────────────────┘
                            │
                            ▼
                    ┌──────────────┐
                    │   Amazon     │
                    │   Bedrock    │
                    │  (AI Model)  │
                    └──────────────┘
```

---

## How a Chat Message Works

1. **You type a question** and press send
2. **The website** verifies your identity (DEFRA Microsoft account) and passes your question to the AI Engine
3. **The AI Engine** checks: did you select a knowledge group?
   - **Yes** → asks the Document Library for the most relevant passages, then sends those plus your question to the AI model
   - **No** → sends your question directly to the AI model
4. **Amazon Bedrock** generates a response
5. **The AI Engine** saves the response; the website displays it

Because AI models take a few seconds, the website shows a loading indicator while it waits.

---

## How Document Upload Works

1. **You upload a file** through the website
2. **The website** passes it to a DEFRA upload service (CDP Uploader) → stored securely in Amazon S3
3. **CDP Uploader** notifies the website the upload is complete
4. **The Document Library** reads the file, breaks it into sections, and creates a mathematical representation (embedding) of each section via Amazon AI
5. Embeddings are stored in a specialist database searchable by meaning rather than keywords
6. Document status changes to "ready" — you can now query it

---

## Sign-In and Security

You sign in using your normal DEFRA Microsoft account — the same one used across DEFRA services. No separate registration needed.

Your documents are personal — only you can see and query them. All communication is encrypted.

---

## Where It Runs

On DEFRA's Core Delivery Platform (CDP), using Amazon Web Services for storage, messaging, and AI. All data stays within DEFRA's approved cloud environment.

See [Security & Privacy](Stakeholder-Security-Privacy) for more detail.
