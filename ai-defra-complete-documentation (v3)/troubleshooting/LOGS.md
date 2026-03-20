# Understanding Logs

**How to read and interpret log messages**

---

## Log Levels

- **ERROR** - Something failed
- **WARN** - Something unexpected
- **INFO** - Normal operations
- **DEBUG** - Detailed diagnostic info

---

## Common Log Messages

### Connection refused

```
Error: Connection refused on port 8086
```

**Meaning:** Service hasn't started or crashed  
**Fix:** `docker compose up -d` and wait 30 seconds

### Timeout

```
Error: Operation timed out after 30s
```

**Meaning:** Service too slow or hung  
**Fix:** Check CPU/memory usage

### Authentication failed

```
Error: Authentication failed for database
```

**Meaning:** Wrong credentials in env  
**Fix:** Check environment variables

---

## Viewing Logs

```bash
# All services
docker compose logs

# Specific service
docker compose logs agent

# Follow in real-time
docker compose logs -f

# Last N lines
docker compose logs --tail=50

# Since timestamp
docker compose logs --since 2024-03-20T10:00:00
```

---

## See Also

- [COMMON-ISSUES.md](./COMMON-ISSUES.md)
- [DEBUGGING.md](./DEBUGGING.md)
