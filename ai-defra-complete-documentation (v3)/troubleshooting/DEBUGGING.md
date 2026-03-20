# Debugging Guide

**How to debug issues in detail**

---

## Check Service Status

```bash
docker compose ps
docker compose logs -f [service-name]
```

---

## Health Checks

```bash
# Agent
curl http://localhost:8086/health

# Knowledge
curl http://localhost:8085/health
```

---

## Database Debugging

### PostgreSQL

```bash
# Connect to database
docker compose exec postgres psql -U postgres

# List databases
\l

# Connect to app database
\c app_db

# List tables
\dt
```

### MongoDB

```bash
docker compose exec mongo mongosh

# List databases
show dbs

# Use database
use app_db

# List collections
show collections
```

---

## API Debugging

```bash
# Test endpoint with verbose output
curl -v http://localhost:8086/health

# Test with headers
curl -v -H "Content-Type: application/json" http://localhost:8086/chat
```

---

## Log Analysis

```bash
# Follow logs
docker compose logs -f

# Specific service
docker compose logs -f agent

# Last 100 lines
docker compose logs --tail=100
```

---

## See Also

- [COMMON-ISSUES.md](./COMMON-ISSUES.md)
- [LOGS.md](./LOGS.md)
