# Code Examples

**Working code and configuration examples**

---

## Contents

1. [API-REQUESTS.md](./API-REQUESTS.md) - API call examples
2. [DOCKER-COMPOSE.md](./DOCKER-COMPOSE.md) - Docker setup
3. [ENV-VARIABLES.md](./ENV-VARIABLES.md) - Configuration

---

## Quick Copy-Paste Examples

### Test Chat API

```bash
curl -X POST http://localhost:8086/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello"}'
```

### Test Search API

```bash
curl -X POST http://localhost:8085/rag/search \
  -H "Content-Type: application/json" \
  -d '{"query": "test"}'
```

---

## See Also

- [API-REQUESTS.md](./API-REQUESTS.md)
- [DOCKER-COMPOSE.md](./DOCKER-COMPOSE.md)
- [ENV-VARIABLES.md](./ENV-VARIABLES.md)
