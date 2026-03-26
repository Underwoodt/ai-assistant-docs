# Design Patterns

> **Source:** Codebase structure and architecture analysis

---

## RAG Pipeline (Retrieval-Augmented Generation)

The defining pattern of the system. Rather than sending a user's question directly to an LLM, the system first retrieves relevant document passages (chunks) from a vector store, then sends those passages as context alongside the question. This grounds the LLM's response in real documents and prevents hallucination.

```
Question → Embed query → Vector similarity search → Retrieve chunks → LLM (question + chunks) → Grounded answer
```

The similarity threshold (default 0.5) acts as a quality gate — chunks below the threshold are excluded even if they are the closest match.

---

## Asynchronous Chat via Queue

Chat requests are not processed synchronously. `POST /chat` returns immediately with a 202 Accepted, enqueues the job to SQS, and the worker processes it in the background. The Frontend polls `GET /conversations/{id}` to get the result.

This decouples request acceptance from processing, improves resilience, and allows batch processing of multiple chat jobs.

---

## Service-per-Concern Microservices

Each service has a single, well-defined responsibility: the Knowledge service knows about documents; the Agent service knows about conversations and LLM interaction; the Frontend knows about rendering and user sessions. They communicate over HTTP with API key authentication.

---

## AI-Agnostic LLM Integration

The system abstracts away the specific LLM via the AWS Bedrock Converse API. The model ID is passed in each chat request, allowing the user to switch between supported LLMs (different Claude versions, etc.) without code changes. Adding a new model is a configuration change, not a code change.

---

## Configuration via Environment Variables

All service configuration is externalised to environment variables (12-factor app style). No secrets or environment-specific values are in source code. Local development uses `.env` files; production uses platform-injected secrets.

---

## Layered Testing

Each service follows a consistent testing pattern: unit tests with high coverage (90%+), integration tests using testcontainers for database-dependent code, and end-to-end journey tests at the system level. Code quality is enforced at every layer via linting, pre-commit hooks, and CI gates.
