# Installation Guide

**Step-by-step setup for local development**

---

## Step 1: Clone Core Repository

```bash
git clone https://github.com/DEFRA/ai-defra-search-core.git
cd ai-defra-search-core
```

[Source: ai-defra-search-core](https://github.com/DEFRA/ai-defra-search-core)

---

## Step 2: Clone All Service Repositories

The core repo includes a script to clone all services:

```bash
# Install Python dependencies
python3.12 -m venv venv
source venv/bin/activate
pip install uv

# Clone all services
uv run task clone
```

This creates:
```
services/
├── ai-defra-search-frontend/
├── ai-defra-search-agent/
├── ai-defra-search-knowledge/
├── ai-defra-search-journey-tests/
├── ai-defra-search-perf-tests/
└── ai-defra-search-aws-bedrock-stub/
```

---

## Step 3: Start Services

### Start All Services (includes databases)

```bash
docker compose up -d
```

[Source: ai-defra-search-core/compose.yml](https://github.com/DEFRA/ai-defra-search-core/blob/main/compose.yml)

### What Starts

| Service | Port | Status |
|---------|------|--------|
| Frontend | 3000 | ✅ React app |
| Agent | 8086 | ✅ Chat API |
| Knowledge | 8085 | ✅ Search API |
| Traefik | 8080 | ✅ Proxy |
| PostgreSQL | 5432 | ✅ Vectors |
| MongoDB | 27017 | ✅ Metadata |
| Redis | 6379 | ✅ Cache |
| LocalStack | 4566 | ✅ AWS mock |

### Wait for Health

```bash
# Wait for services to be ready (usually 30-60 seconds)
docker compose exec agent curl http://localhost:8086/health
docker compose exec knowledge curl http://localhost:8085/health
```

---

## Step 4: Access the Application

**Frontend:** http://frontend.localhost

⚠️ **IMPORTANT:** Use `frontend.localhost` NOT `localhost:3000`
- Using localhost:3000 breaks AWS file upload routing

[Source: ai-defra-search-frontend/README.md](https://github.com/DEFRA/ai-defra-search-frontend/blob/main/README.md)

---

## Step 5: Verify Everything

→ Go to [VERIFICATION.md](./VERIFICATION.md)

---

## Troubleshooting Installation

**Port already in use?**
```bash
docker compose down
docker compose up -d
```

**Services won't start?**
```bash
docker compose logs -f
```

**See [../troubleshooting/](../troubleshooting/) for more help**

---

## Environment Configuration

⚠️ **UNVERIFIED:** Custom environment configuration options not fully documented

For advanced configuration:
- Copy `.env.example` to `.env`
- Edit values as needed
- See [../examples/ENV-VARIABLES.md](../examples/ENV-VARIABLES.md)

---

## Next Steps

1. ✅ Services running
2. → [VERIFICATION.md](./VERIFICATION.md) - Confirm setup
3. → [../api-reference/EXAMPLES.md](../api-reference/EXAMPLES.md) - Try APIs
4. → [../architecture/](../architecture/) - Understand system
