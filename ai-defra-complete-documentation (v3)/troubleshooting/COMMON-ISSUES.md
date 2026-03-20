# Common Issues

**Solutions for common problems**

---

## Services Won't Start

**Problem:** `docker compose up` fails

**Solutions:**

1. Check Docker running: `docker ps`
2. Check disk space: `df -h`
3. Check ports available: `lsof -i :8086`
4. Clean and restart:
```bash
docker compose down -v
docker compose up -d
```

---

## Port Already in Use

**Problem:** "Address already in use"

**Solution:**
```bash
# Find what's using the port
lsof -i :8086

# Kill the process or use different port
docker compose down
docker compose up -d
```

---

## Database Connection Errors

**Problem:** Services can't connect to PostgreSQL/MongoDB

**Solutions:**
1. Check database containers: `docker compose ps`
2. Verify environment variables
3. Check logs: `docker compose logs postgres`
4. Restart database: `docker compose restart postgres`

---

## API Not Responding

**Problem:** `/health` endpoint timeout

**Solutions:**
1. Check service running: `docker compose ps agent`
2. Check logs: `docker compose logs agent`
3. Verify it's fully started (60+ seconds)
4. Check network: `docker network ls`

---

## Memory Issues

**Problem:** Services crash or OOM

**Solutions:**
1. Check Docker memory limit
2. Reduce load
3. Review logs for memory leaks

---

## Network Issues

**Problem:** Frontend.localhost not resolving

**Solutions:**
1. Check /etc/hosts file
2. Use http://localhost:3000 as fallback (but breaks uploads)
3. Restart networking

---

## See Also

- [DEBUGGING.md](./DEBUGGING.md) - Deeper debugging
- [LOGS.md](./LOGS.md) - Understanding logs
