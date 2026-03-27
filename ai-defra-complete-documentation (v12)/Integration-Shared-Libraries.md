# Shared Libraries & Contracts

> 🔧 **Integration & Deployment** — [Home](Home) › [Integration & Deployment](Home#integration--deployment)

---

## Overview

Services do not share a common library or SDK. Integration is through HTTP/REST contracts and shared environment conventions rather than shared code.

---

## Shared Conventions

### API Key Authentication

```
Header: X-API-KEY: <service-specific-key>
Header: user-id: <user-uuid>   ← where user identity is needed
```

### Health Endpoint Convention

```
GET /health  →  200 OK  {"status": "ok"}
```

### Environment Variable Naming

| Pattern | Example |
|---|---|
| `*_SERVICE_URL` | `KNOWLEDGE_SERVICE_URL`, `AGENT_SERVICE_URL` |
| `*_API_KEY` | `KNOWLEDGE_API_KEY`, `AGENT_API_KEY` |
| `AWS_REGION` | Consistent across all services |
| `CLOUDWATCH_ENABLED` | Boolean toggle, Agent and Knowledge |

---

## API Contracts

### Agent → Knowledge: RAG Search

```json
POST /rag/search
{
  "query": "user question",
  "knowledge_group_ids": ["uuid"],
  "max_results": 5
}
```

**Response:**
```json
[{ "content": "chunk text", "file_name": "doc.pdf", "s3_key": "...", "score": 0.92 }]
```

### Frontend → Agent: Chat

```json
POST /chat
{
  "question": "string",
  "conversation_id": "uuid (optional)",
  "model_id": "anthropic.claude-3-5-sonnet-20241022-v2:0",
  "knowledge_group_ids": ["uuid"]
}
```

**Response:** `202 { "conversation_id": "uuid", "message_id": "uuid", "status": "queued" }`

### CDP Uploader → Frontend: Callback

Fires when upload completes. Includes S3 key and the `uploadReference` UUID issued at initiation.

---

## Python Tooling (Agent + Knowledge)

| Tool | Purpose |
|---|---|
| `uv` | Package management and virtual environments |
| `Ruff` | Linting and formatting |
| `pytest` | Testing |
| `taskipy` | Task runner (`uv run task lint/test`) |
| `Trivy` | Container and dependency vulnerability scanning |
| `SonarCloud` | Code quality and security analysis |

---

## Node.js Tooling (Frontend + Journey Tests)

| Tool | Purpose |
|---|---|
| `npm` | Package management |
| `ESLint` / `Prettier` / `Stylelint` | Linting and formatting |
| `Husky` | Pre-commit hooks |
| `Trivy` | Container scanning |
| `SonarCloud` | Code quality analysis |
