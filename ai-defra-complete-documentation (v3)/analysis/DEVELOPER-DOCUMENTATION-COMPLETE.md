# Complete Developer Analysis

**Comprehensive documentation of all 7 DEFRA AI Search repositories**

---

## 📊 All 7 Repositories

| # | Repository | Status | Docs |
|---|-----------|--------|------|
| 1 | [ai-defra-search-core](https://github.com/DEFRA/ai-defra-search-core) | ✅ | Infrastructure |
| 2 | [ai-defra-search-frontend](https://github.com/DEFRA/ai-defra-search-frontend) | ✅ | React UI |
| 3 | [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent) | ✅ | Chat API |
| 4 | [ai-defra-search-knowledge](https://github.com/DEFRA/ai-defra-search-knowledge) | ✅ | Search/Ingest |
| 5 | [ai-defra-search-journey-tests](https://github.com/DEFRA/ai-defra-search-journey-tests) | ✅ | E2E Tests |
| 6 | [ai-defra-search-perf-tests](https://github.com/DEFRA/ai-defra-search-perf-tests) | ✅ | Perf Tests |
| 7 | [ai-defra-search-aws-bedrock-stub](https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub) | ✅ | Mock LLM |

---

## ✅ Verified

### API Endpoints (Extracted from code)

**Agent Service (Port 8086)**
- POST /chat [Source: ai-defra-search-agent/app/entrypoints/](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/entrypoints/)
- GET /health
- POST /feedback

**Knowledge Service (Port 8085)**
- POST /documents
- POST /rag/search
- GET /knowledge-groups

[Source: ai-defra-search-knowledge/app/](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/app/)

### Resilience Patterns (Verified)

**Retry Logic**
[Source: ai-defra-search-agent/app/common/mongo.py#L28-L37](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/common/mongo.py)

**Timeouts**
[Source: ai-defra-search-knowledge/app/common/postgres.py#L62](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/app/common/postgres.py)

### Dependencies (From source files)

[Source: ai-defra-search-agent/pyproject.toml](https://github.com/DEFRA/ai-defra-search-agent/blob/main/pyproject.toml)
- fastapi==0.121.2
- pydantic==2.12.4
- motor==3.7.1
- boto3==1.42.30
- anthropic==0.64.0

[Source: ai-defra-search-knowledge/pyproject.toml](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/pyproject.toml)
- sqlalchemy==2.0.23
- pgvector==0.2.4
- PyMuPDF==1.23.8
- python-docx==0.8.11
- python-pptx==0.6.21

---

## ⚠️ Unverified

### Performance Statistics

❌ **"2-3 second response time"** - NOT FOUND IN SOURCE

Found instead:
- Mock Bedrock has 2000ms delay (for testing only)
- JMeter timeout config: 20,000-30,000 ms (test config, not requirement)

**To verify:** Check production monitoring dashboards or production testing

### Scaling Strategy

⚠️ **NOT DOCUMENTED**

Found:
- Single-instance Docker Compose setup
- Traefik reverse proxy (single instance)

NOT found:
- Auto-scaling configuration
- Kubernetes HPA setup
- Load balancing strategy
- Database replication

**To verify:** Check Kubernetes manifests or CDPs documentation

### Design Decisions

❌ **NOT FOUND IN SOURCE**

Questions without documented answers:
- Why PostgreSQL + pgvector vs. dedicated vector DB?
- Why FastAPI?
- Why MongoDB for metadata?

**To verify:** Check pull requests or ADRs (Architecture Decision Records)

### Service Interaction Patterns

⚠️ **PARTIALLY DOCUMENTED**

Found:
- httpx AsyncClient [Source: ai-defra-search-agent/app/common/http_client.py](https://github.com/DEFRA/ai-defra-search-agent/blob/main/app/common/http_client.py)
- Timeout configuration

NOT found:
- Explicit service communication patterns
- Circuit breaker implementation
- Bulkhead isolation
- Retry strategies between services

---

## 🔗 All Repository Links

1. https://github.com/DEFRA/ai-defra-search-core
2. https://github.com/DEFRA/ai-defra-search-frontend
3. https://github.com/DEFRA/ai-defra-search-agent
4. https://github.com/DEFRA/ai-defra-search-knowledge
5. https://github.com/DEFRA/ai-defra-search-journey-tests
6. https://github.com/DEFRA/ai-defra-search-perf-tests
7. https://github.com/DEFRA/ai-defra-search-aws-bedrock-stub

---

**For more detailed analysis, see other files in subdirectories**

**See also:**
- [../setup/](../setup/) - Installation guides
- [../api-reference/](../api-reference/) - API documentation
- [../architecture/](../architecture/) - System architecture
- [../deployment/](../deployment/) - Deployment guides
- [../examples/](../examples/) - Code examples
