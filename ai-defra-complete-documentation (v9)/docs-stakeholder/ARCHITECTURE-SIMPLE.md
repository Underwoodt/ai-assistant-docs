# How It Works — Plain English

[← Back to Stakeholder Docs](README.md)

---

## The Four Parts of the System

The tool is made up of four components that work together:

**The Chat Interface** is the website that users interact with. It looks like a messaging app — you type a question and receive an answer. It also lets you upload documents and manage your personal document groups. Access is controlled via your DEFRA Microsoft account (Entra ID), so only authorised users can log in.

**The Assistant** is the brain behind the chat. When you ask a question, it decides whether to search your documents for relevant passages or answer from the AI model's general knowledge — then calls the AI model to compose a clear response. It keeps track of the conversation so follow-up questions work correctly.

**The Document Library** stores your uploaded documents. When you upload a file, the Library breaks it into passages, converts each passage into a mathematical form that captures its meaning, and stores it so it can be searched quickly. It also records the status of each document (uploading, processing, ready). When the Assistant asks "what's relevant to this question?", the Library finds and returns the best-matching passages.

**The AI Service** is the large language model (LLM) that reads the question and any relevant passages and writes the response. The system uses AWS Bedrock, which is DEFRA's approved cloud AI service. In local development, a built-in stand-in replaces this so developers can work without an internet connection.

---

## How a Question Gets Answered

```
You type a question
      ↓
The Assistant receives it and queues it for processing
      ↓
The Assistant decides whether your documents are relevant to this question
      ↓
If yes: The Document Library finds the most relevant passages
      ↓
The Assistant sends your question (+ any relevant passages) to the AI model
      ↓
The AI writes a response — grounded in your documents where relevant,
or from its general training knowledge where not
      ↓
The answer appears in your chat window
```

Responses appear in full once ready — they do not appear word by word.

---

## How Your Documents Are Uploaded

DEFRA uses a managed upload service (CDP Uploader) to handle document storage securely:

1. You select a file in the interface and confirm the upload
2. The CDP Uploader stores the file securely in cloud storage (AWS S3) and confirms completion
3. The system is notified and begins processing the document automatically
4. The document Library extracts the text, breaks it into passages, and makes them searchable

You can see the status of each document (processing / ready) in your knowledge groups.

---

## What Happens to Your Documents

When you upload a document:
- The text is extracted and divided into manageable passages
- Each passage is converted into a mathematical form (a "vector") that captures its meaning — using a model called Titan Embed v2
- The passages and their vectors are stored in a secure database
- The original file is stored in secure cloud storage (S3)
- Document metadata (filename, status, knowledge group) is stored separately

When you ask a question, the system converts your question into the same mathematical form and finds the passages whose meanings are closest to it. Only passages above a relevance threshold are used.

---

## Where the Data Lives

All data is stored within DEFRA's infrastructure on AWS, running on the Core Delivery Platform (CDP). User authentication is handled by DEFRA's Microsoft Entra ID — the same login used for other DEFRA systems. Nothing is shared with external AI providers beyond the specific query text and retrieved document passages needed to answer each question.
