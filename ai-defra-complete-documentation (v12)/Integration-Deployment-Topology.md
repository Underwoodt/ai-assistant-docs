# Deployment Topology

> 🔧 **Integration & Deployment** — [Home](Home) › [Integration & Deployment](Home#integration--deployment)

---

## Local Development (Docker Compose)

```
Host Machine
├── Browser → http://frontend.localhost
│
└── Docker Network
    ├── traefik:80          ← Routes frontend.localhost → frontend:3000
    ├── frontend:3000       ← Hapi/Node.js
    ├── agent:8086          ← FastAPI (Python)
    ├── knowledge:8085      ← FastAPI (Python)
    ├── wiremock:8080       ← Bedrock Stub
    ├── mongodb:27017       ← MongoDB 6.0.13
    ├── postgres:5432       ← PostgreSQL + pgvector
    ├── redis:6379          ← Redis 7.2.3
    └── localstack:4566     ← AWS emulation (S3, SQS)
```

---

## CDP (Production / Staging)

```
Internet
    │ HTTPS
CDP Ingress / Load Balancer
    │
    ├── frontend (Node.js container)
    │       ├── → agent (internal HTTP + API key)
    │       ├── → knowledge (internal HTTP + API key)
    │       ├── → CDP Uploader (DEFRA managed)
    │       └── ← CDP Uploader callback
    │
    ├── agent (Python container)
    │       ├── → knowledge (internal HTTP + API key)
    │       ├── → AWS Bedrock (HTTPS, IAM role)
    │       ├── ↔ AWS SQS (HTTPS, IAM role)
    │       └── ↔ MongoDB (CDP-managed)
    │
    └── knowledge (Python container)
            ├── → AWS Bedrock Titan Embed (HTTPS, IAM role)
            ├── → AWS S3 (HTTPS, IAM role)
            ├── ↔ MongoDB (CDP-managed)
            └── ↔ PostgreSQL + pgvector (CDP-managed)
```

---

## Container Ports Reference

| Service | Internal | External (local) | Protocol |
|---|---|---|---|
| Frontend | 3000 | `frontend.localhost:80` via Traefik | HTTP |
| Agent | 8086 | `localhost:8086` | HTTP |
| Knowledge | 8085 | `localhost:8085` | HTTP |
| Bedrock Stub | 8080 | `localhost:8090` | HTTP |
| MongoDB | 27017 | `localhost:27017` | TCP |
| PostgreSQL | 5432 | `localhost:5432` | TCP |
| Redis | 6379 | `localhost:6379` | TCP |
| LocalStack | 4566 | `localhost:4566` | HTTP |
| Agent Debugger | 5679 | `localhost:5679` | TCP (debug only) |

---

## Database Ownership

| Database | Owner Service | Contents |
|---|---|---|
| MongoDB — Agent DB | Agent Service | `conversations`, `messages` |
| MongoDB — Knowledge DB | Knowledge Service | `knowledge_groups`, `documents` |
| PostgreSQL | Knowledge Service | `knowledge_vectors` (pgvector) |
| Redis | Frontend | Short-lived conversation cache (TTL-based) |
