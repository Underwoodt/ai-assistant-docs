# Frontend Service

[← Back to Developer Docs](../README.md)

**Repo:** [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend)

---

## Overview

Node.js / Hapi / Nunjucks server-rendered web application providing the browser-based chat UI. Handles user authentication via Microsoft Entra ID, manages conversation caching via Redis, drives document upload via CDP Uploader, and receives upload callbacks before notifying the Knowledge Service.

## Stack

| Technology | Role |
|---|---|
| Node.js (≥ 22) | Runtime |
| Hapi | Web framework |
| Nunjucks | Server-side HTML templating |
| SCSS / Webpack / Vite | Styling and asset bundling |
| Redis (Catbox) | Server-side conversation cache |
| Microsoft Entra ID | User authentication (OAuth 2.0) |
| CDP Uploader | File-to-S3 upload with callback |
| LocalStack | Local AWS emulation |
| Vitest | Unit testing |

## Ports & Routing

| URL | Description |
|---|---|
| `http://frontend.localhost` | Access via Traefik proxy (recommended) |
| `http://localhost:3000` | Direct port (not recommended — bypasses Traefik) |

## Key User Flows

### Chat

1. User submits a prompt on the chat UI
2. Frontend `POST /chat` to Agent (with `X-API-KEY` + `user-id`)
3. Agent returns `202 { conversation_id, message_id, status: queued }`
4. Frontend redirects to conversation view and polls `GET /conversations/{id}`
5. UI updates when response is complete; result cached in Redis

### Document Upload

1. User selects a knowledge group and uploads a file
2. Frontend calls CDP Uploader `POST /initiate` → receives `uploadReference` UUID
3. User's file is sent to CDP Uploader → stored in S3
4. CDP Uploader fires callback to Frontend
5. Frontend calls Knowledge Service `POST /knowledge-groups/{id}/documents` with S3 key and metadata

## Caching Strategy

Conversation data is cached in Redis with TTL-based cleanup. This provides resilience during API downtime — the user can still see previous responses even if the Agent is temporarily unavailable.

In local development, an in-memory Catbox cache is used instead of Redis.

## Environment Variables

| Variable | Description |
|---|---|
| `AGENT_SERVICE_URL` | Agent Service base URL |
| `AGENT_API_KEY` | API key for Agent Service |
| `KNOWLEDGE_SERVICE_URL` | Knowledge Service base URL |
| `KNOWLEDGE_API_KEY` | API key for Knowledge Service |
| `ENTRA_CLIENT_ID` | Microsoft Entra ID OAuth client ID |
| `ENTRA_CLIENT_SECRET` | Microsoft Entra ID OAuth client secret |
| `ENTRA_TENANT_ID` | Microsoft Entra ID tenant ID |
| `REDIS_URI` | Redis connection string |
| `SESSION_SECRET` | Session signing secret (min. 32 bytes) |
| `CDP_UPLOADER_URL` | CDP Uploader service URL |

## Running Locally

Must run within the core meta-repo:

```bash
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core
npm install && cp .env.example .env
docker compose up
```

Access at `http://frontend.localhost`

## Testing

```bash
npm test      # Vitest unit tests
npm run lint  # ESLint
```
