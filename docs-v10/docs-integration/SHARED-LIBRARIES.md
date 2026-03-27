# Shared Libraries & Contracts

[← Back to Integration Docs](README.md)

---

## Overview

The services in this project do not share a common library or SDK package. Each service is independently deployable and manages its own dependencies. Integration is achieved through HTTP/REST contracts and environment configuration conventions rather than shared code.

---

## Shared Conventions

### API Key Authentication

All inter-service HTTP calls use a shared convention:

```
Header: X-API-KEY: <service-specific-key>
Header: user-id: <user-uuid>   ← where user identity is needed
```

Services validate the `X-API-KEY` header against their configured key on every request.

### Health Endpoint Convention

All services expose:

```
GET /health
→ 200 OK {"status": "ok"}
```

Used for container liveness checks in Docker Compose and CDP health monitoring.

### Environment Variable Naming

Services follow consistent naming patterns for shared infrastructure:

| Pattern | Example |
|---|---|
| `*_SERVICE_URL` | `KNOWLEDGE_SERVICE_URL`, `AGENT_SERVICE_URL` |
| `*_API_KEY` | `KNOWLEDGE_API_KEY`, `AGENT_API_KEY` |
| `AWS_REGION` | Consistent across all services |
| `CLOUDWATCH_ENABLED` | Boolean toggle on Agent and Knowledge |

---

## Data Contracts

### Agent → Knowledge: RAG Search Request

```json
POST /rag/search
{
  "query": "string (user question)",
  "knowledge_group_ids": ["uuid", "..."],
  "max_results": 5
}
```

**Response:**

```json
[
  {
    "content": "string (chunk text)",
    "file_name": "string",
    "s3_key": "string",
    "score": 0.92
  }
]
```

### Frontend → Agent: Chat Request

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

### CDP Uploader Callback → Frontend

The CDP Uploader fires a callback to the Frontend when an upload completes. The callback includes the S3 key and the `uploadReference` UUID originally issued at initiation.

---

## Python Tooling (Agent + Knowledge)

Both Python services use the same toolchain:

| Tool | Purpose |
|---|---|
| `uv` | Package management and virtual environments |
| `Ruff` | Linting and code formatting |
| `pytest` | Testing framework |
| `taskipy` | Task runner (`uv run task lint/test`) |
| `Trivy` | Container and dependency vulnerability scanning |
| `SonarCloud` | Code quality and security analysis |

Their `pyproject.toml` structures follow the same conventions, making it straightforward to apply changes (e.g. updating a shared dependency) consistently across both.

---

## Node.js Tooling (Frontend + Journey Tests)

| Tool | Purpose |
|---|---|
| `npm` | Package management |
| `ESLint` | JavaScript linting |
| `Prettier` / `Stylelint` | Code formatting |
| `Husky` | Pre-commit hooks |
| `Trivy` | Container scanning |
| `SonarCloud` | Code quality analysis |
