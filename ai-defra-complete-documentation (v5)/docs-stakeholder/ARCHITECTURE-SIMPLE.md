# How It Works — Plain English

---

## The Four Parts of the System

The tool is made up of four components that work together:

**The Chat Interface** is the website that users interact with. It looks like a messaging app — you type a question and receive an answer. It also lets you upload documents and manage your document groups.

**The Assistant** is the brain behind the chat. When you ask a question, it searches your documents for relevant passages, then passes those passages to an AI language model (like Claude) to compose a clear answer. It also keeps track of the conversation so follow-up questions work correctly.

**The Document Library** stores your uploaded documents. When you upload a file, the Library breaks it into passages, generates a mathematical representation of the meaning of each passage, and stores it so it can be searched quickly. When the Assistant asks "what's relevant to this question?", the Library finds and returns the best-matching passages.

**The AI Service** is the large language model (LLM) that reads the question and the relevant passages and writes the response. By default in development, this is a local stand-in so developers do not need a live internet connection.

---

## How a Question Gets Answered

```
You type a question
      ↓
The Assistant receives it and queues it for processing
      ↓
The Document Library finds the most relevant passages from your documents
      ↓
The Assistant sends your question + those passages to the AI model
      ↓
The AI writes an answer based only on the passages provided
      ↓
The answer appears in your chat window
```

This process typically takes a few seconds. The response does not appear word-by-word — it appears in full once ready.

---

## What Happens to Your Documents

When you upload a document:
1. The text is extracted and divided into manageable passages
2. Each passage is converted into a mathematical form that captures its meaning
3. The passages and their mathematical forms are stored in a secure database
4. The original file is also stored

When you ask a question, the question is converted into the same mathematical form, and the passages whose meanings are closest to your question are retrieved. Only passages above a relevance threshold are used — the tool will not return weakly related content.

---

## Where the Data Lives

All data (documents, conversation history, embeddings) is stored within DEFRA's infrastructure. In production, this runs on DEFRA's Common Deployment Platform (CDP) using AWS services. Nothing is sent to external AI services beyond the query and retrieved document passages — your documents are not used to train any external AI model.
