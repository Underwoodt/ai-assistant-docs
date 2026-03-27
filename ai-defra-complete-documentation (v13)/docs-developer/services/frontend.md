# Frontend Service

[← Back to Developer Docs](../README.md)



**Repo:** [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend) · Access: `http://frontend.localhost`

---

## Overview

Node.js / Hapi / Nunjucks server-rendered web app. Handles user auth via Microsoft Entra ID, manages conversation caching in Redis, drives file upload via CDP Uploader, and receives upload callbacks before notifying the Knowledge Service.

## Stack

| Technology | Role |
|---|---|
| Node.js 22, Hapi, Nunjucks | Server and templating |
| SCSS, Webpack, Vite | Styling and asset bundling |
| Redis (Catbox) | Server-side conversation cache |
| Microsoft Entra ID | OAuth 2.0 user authentication |
| CDP Uploader | Virus-checked file upload to S3 |
| Vitest | Unit testing |

## Key Flows

### Chat
1. User submits prompt → Frontend `POST /chat` to Agent
2. Agent returns `202 { conversation_id, message_id, status: queued }`
3. Frontend shows loading spinner, polls `GET /conversations/{id}`
4. UI updates when response completes; cached in Redis

### Upload
1. User selects file → Frontend `POST /initiate` to CDP Uploader → receives `uploadReference`
2. File uploaded to CDP Uploader → stored in S3
3. CDP Uploader fires callback to Frontend
4. Frontend calls Knowledge `POST /knowledge-groups/{id}/documents`

## Caching
Conversation data is cached in Redis with TTL-based cleanup. In-memory Catbox cache is used locally. Provides resilience during brief API outages.

## Key Environment Variables

| Variable | Description |
|---|---|
| `AGENT_SERVICE_URL` / `AGENT_API_KEY` | Agent service connection |
| `KNOWLEDGE_SERVICE_URL` / `KNOWLEDGE_API_KEY` | Knowledge service connection |
| `ENTRA_CLIENT_ID/SECRET/TENANT_ID` | Entra ID OAuth config |
| `REDIS_URI` | Redis connection |
| `SESSION_SECRET` | Session signing secret |
| `CDP_UPLOADER_URL` | CDP Uploader URL |

## Running Locally

Must run inside the core meta-repo:

```bash
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core
npm install && cp .env.example .env
docker compose up
# Access: http://frontend.localhost
```

## Testing

```bash
npm test      # Vitest
npm run lint  # ESLint
```
