# Architecture Documentation

**System design and technical architecture**

---

## Contents

1. [SYSTEM-DESIGN.md](./SYSTEM-DESIGN.md) - High-level overview
2. [DATA-FLOWS.md](./DATA-FLOWS.md) - How data moves
3. [SERVICE-INTERACTIONS.md](./SERVICE-INTERACTIONS.md) - Service communication

---

## Quick Overview

### Main Components

```
Frontend (React, 3000)
    ↓ (via Traefik)
Agent API (FastAPI, 8086)  ← Chat endpoint
    ↓
Knowledge API (FastAPI, 8085) ← Search endpoint
    ↓
Database layer:
  • PostgreSQL (vectors)
  • MongoDB (metadata)
  • Redis (cache)
  • S3/LocalStack (files)
```

---

## Key Points

✅ **Verified:**
- Service ports and frameworks
- Docker Compose setup
- Database connections

⚠️ **Unverified:**
- Scaling strategy
- Resilience patterns (partial)
- Design decisions
- Trade-offs

---

## Read More

- [SYSTEM-DESIGN.md](./SYSTEM-DESIGN.md)
- [DATA-FLOWS.md](./DATA-FLOWS.md)
- [SERVICE-INTERACTIONS.md](./SERVICE-INTERACTIONS.md)
