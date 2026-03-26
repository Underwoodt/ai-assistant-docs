# How It Works

[← Back to Stakeholder Docs](README.md)

---

## The Big Picture

The AI DEFRA Search Assistant is made up of three main parts that work together: a website you interact with in your browser, an AI reasoning engine that handles your questions, and a document library that stores and searches your uploaded files.

```
Your Browser
    │
    ▼
┌──────────────┐     ┌──────────────────┐     ┌──────────────────────┐
│   Website    │────▶│   AI Engine      │────▶│   Document Library   │
│  (Frontend)  │     │  (Agent Service) │     │ (Knowledge Service)  │
└──────────────┘     └──────────────────┘     └──────────────────────┘
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

1. **You type a question** in the browser and press send.
2. **The website** logs you in via your DEFRA Microsoft account, then passes your question to the AI Engine.
3. **The AI Engine** checks: did you select a knowledge group (document collection) for this conversation?
   - **Yes** → it asks the Document Library for the most relevant passages from your documents, then sends those passages along with your question to the AI model.
   - **No** → it sends your question directly to the AI model without searching documents.
4. **Amazon Bedrock** generates a response and returns it.
5. **The AI Engine** saves the response, and the website displays it.

Because AI models can take a few seconds to respond, the website shows a loading indicator while it waits, then displays the answer when it arrives.

---

## How Document Upload Works

1. **You upload a file** through the website.
2. **The website** passes the file to a DEFRA file upload service (CDP Uploader), which stores it securely in cloud storage (Amazon S3).
3. **CDP Uploader** notifies the website that the upload is complete.
4. **The website** tells the Document Library that a new file is ready.
5. **The Document Library** reads the file, breaks it into sections, and generates a mathematical representation (embedding) of each section using the Amazon AI service.
6. These embeddings are stored in a specialist database so they can be quickly searched by meaning rather than by keywords.
7. Once complete, the document status changes to "ready" — and you can start asking questions about it.

---

## Sign-In and Security

You sign in using your normal DEFRA Microsoft account (Microsoft Entra ID) — the same account you use for other DEFRA services. No separate password or account is required.

Your documents are personal — only you can see and query your own uploaded files. Communication between all parts of the system is encrypted.

---

## Where It Runs

The system runs on DEFRA's Core Delivery Platform (CDP), using Amazon Web Services for cloud storage, messaging, and AI model access. All data remains within DEFRA's approved cloud environment.

See [SECURITY-PRIVACY.md](SECURITY-PRIVACY.md) for more detail on data handling.
