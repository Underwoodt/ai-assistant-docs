# Docker Compose Examples

**Docker configuration examples**

---

## Start All Services

```bash
docker compose up -d
```

[Source: ai-defra-search-core/compose.yml](https://github.com/DEFRA/ai-defra-search-core/blob/main/compose.yml)

---

## Stop All Services

```bash
docker compose down
```

---

## View Service Status

```bash
docker compose ps
```

---

## See Service Logs

```bash
docker compose logs -f [service-name]
```

---

## Services Started

- Frontend (port 3000)
- Agent (port 8086)
- Knowledge (port 8085)
- PostgreSQL (port 5432)
- MongoDB (port 27017)
- Redis (port 6379)

---

## See Also

- [../deployment/DOCKER.md](../deployment/DOCKER.md)
- [../setup/INSTALLATION.md](../setup/INSTALLATION.md)
