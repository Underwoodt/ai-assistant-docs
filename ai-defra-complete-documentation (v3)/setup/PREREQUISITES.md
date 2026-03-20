# Prerequisites

**What you need before starting setup**

---

## System Requirements

### Operating System
- macOS 10.15+
- Ubuntu 18.04+
- Windows 10+ (with WSL2)
- CentOS 7+

### Hardware (Minimum)
- CPU: 4 cores
- RAM: 8 GB
- Disk: 20 GB free

### Hardware (Recommended for development)
- CPU: 8 cores
- RAM: 16 GB
- Disk: 50 GB free

---

## Required Software

### Docker & Docker Compose

**macOS:**
```bash
brew install docker
# Start Docker Desktop app
```

**Ubuntu/Debian:**
```bash
sudo apt-get update
sudo apt-get install docker.io docker-compose
sudo usermod -aG docker $USER
```

**Verify installation:**
```bash
docker --version
docker-compose --version
```

### Python

**Required:** Python 3.12 or higher

**macOS:**
```bash
brew install python@3.12
python3 --version
```

**Ubuntu/Debian:**
```bash
sudo apt-get install python3.12 python3.12-venv
python3.12 --version
```

### Git

```bash
git --version  # Should be 2.25+
```

---

## Optional Tools

### For Better Development

- VS Code with Python/Docker extensions
- IntelliJ IDEA
- Postman (API testing)
- JMeter (performance testing)

### For macOS M1/M2

Special note: Architecture-specific Docker images may be needed.

⚠️ **UNVERIFIED:** Specific M1 compatibility notes not found in source

---

## Estimated Disk Space

[Source: Repositories on GitHub](https://github.com/DEFRA/ai-defra-search-core)

- Core repo: ~50 MB
- Frontend repo: ~100 MB
- Agent repo: ~150 MB
- Knowledge repo: ~200 MB
- Other repos: ~50 MB
- Docker images: ~5 GB (first build)
- Local databases: ~2 GB

**Total: ~8-10 GB first time, ~2-3 GB for repos alone**

---

## Network Requirements

- Internet connection for downloading Docker images
- GitHub access (for cloning repos)
- No proxy restrictions (or proxy configured)

---

## For Windows Users

### Install WSL2

```powershell
wsl --install
wsl --update
```

### Use WSL Terminal

All commands should run in WSL2 terminal, not PowerShell.

---

## Ready?

→ [Continue to INSTALLATION.md](./INSTALLATION.md)
