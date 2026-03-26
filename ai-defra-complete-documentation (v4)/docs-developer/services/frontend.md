# Frontend Service

> **Repo:** `ai-defra-search-frontend` · **Port:** 3000 · **Stack:** Node.js 22+, Hapi, Nunjucks

---

## Purpose

The Frontend provides the browser-based chat interface. It renders pages using server-side Nunjucks templates, proxies API calls to the Agent and Knowledge services, handles file uploads, and caches conversation data in Redis.

---

## Key Modules

| Path | Responsibility |
|---|---|
| `src/server/server.js` | Hapi server initialisation and plugin registration |
| `src/server/router.js` | Route registration |
| `src/server/services/chat-service.js` | Proxy calls to Agent `/chat` and `/conversations` |
| `src/server/services/knowledge-groups-service.js` | Proxy calls to Knowledge group endpoints |
| `src/server/services/models-service.js` | Fetch available LLM models from Agent |
| `src/server/services/feedback-service.js` | Submit user feedback to Agent |
| `src/server/services/cdp-uploader-service.js` | File upload handling via CDP uploader |
| `src/server/knowledge/` | Routes and views for knowledge group management |
| `src/server/home/` | Chat home page and conversation rendering |
| `src/server/upload/` | File upload routes |
| `src/server/auth/` | Authentication handling |
| `src/client/javascripts/` | Client-side JavaScript (bundled via Webpack) |
| `src/client/stylesheets/` | SCSS styles |

---

## Caching

Redis (v7.2.3) is used for session and conversation caching via the `@hapi/catbox-redis` plugin. In local development, Redis runs as a Docker Compose service.

---

## Routing Note

> ⚠ The frontend **must be accessed via `frontend.localhost`** when running in the full stack. This is enforced by Traefik routing. Accessing `localhost:3000` directly may produce unexpected behaviour.

---

## Building and Running

```bash
# Development (with hot reload)
npm run dev

# Production build
npm run build:frontend
npm start
```

---

## Testing

```bash
npm test
# Runs: ESLint + Stylelint → Webpack build → Vitest with coverage
```

Test files live in `tests/`. The framework is Vitest. Mockoon is available in `mockoon/` for mocking backend APIs during frontend-only development.

---

## Key Dependencies

| Package | Role |
|---|---|
| `@hapi/hapi` | Web server framework |
| `@hapi/catbox-redis` | Redis session cache |
| `nunjucks` | Server-side HTML templating |
| `webpack` | Client-side JS bundling |
| `vitest` | Unit testing |
| `govuk-frontend` | DEFRA / GOV.UK design system components |
