# API Reference

**Documentation for Agent and Knowledge APIs**

---

## Available APIs

### Agent Service (Chat API)

Port: 8086

**Purpose:** Handle user chat interactions

**Files:**
- [AGENT-API.md](./AGENT-API.md) - Full documentation
- [EXAMPLES.md](./EXAMPLES.md) - Code examples

### Knowledge Service (Search API)

Port: 8085

**Purpose:** Document ingestion and semantic search

**Files:**
- [KNOWLEDGE-API.md](./KNOWLEDGE-API.md) - Full documentation
- [EXAMPLES.md](./EXAMPLES.md) - Code examples

---

## Quick Start

### Test Chat API

```bash
curl -X POST http://localhost:8086/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello"}'
```

### Test Knowledge API

```bash
curl -X POST http://localhost:8085/rag/search \
  -H "Content-Type: application/json" \
  -d '{"query": "test query"}'
```

---

## Documentation

1. [AGENT-API.md](./AGENT-API.md) - Agent API details
2. [KNOWLEDGE-API.md](./KNOWLEDGE-API.md) - Knowledge API details
3. [EXAMPLES.md](./EXAMPLES.md) - Working examples

---

## See Also

- [../setup/](../setup/) - Setup & installation
- [../examples/API-REQUESTS.md](../examples/API-REQUESTS.md) - More examples
