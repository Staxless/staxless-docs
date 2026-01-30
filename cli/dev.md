# staxless dev

> Manage your local development environment.

Wraps Docker Compose with sensible defaults and better output.

---

## Commands

| Command | Description |
|---------|-------------|
| `staxless dev start` | Start with build |
| `staxless dev up` | Start without build |
| `staxless dev down` | Stop all services |
| `staxless dev logs` | View logs |
| `staxless dev status` | Show running services |
| `staxless dev reset` | Nuclear option — full reset |

---

## staxless dev start

Start all services and rebuild images.

```bash
staxless dev start
```

### Options

| Option | Description |
|--------|-------------|
| `-s, --service <name>` | Start only this service |

### Examples

```bash
# Start everything
staxless dev start

# Start only auth service
staxless dev start -s auth-service
```

---

## staxless dev up

Start services without rebuilding (faster).

```bash
staxless dev up
```

### Options

| Option | Description |
|--------|-------------|
| `-s, --service <name>` | Start only this service |
| `-d, --detach` | Run in background |

### Examples

```bash
# Start in background
staxless dev up -d

# Start frontend only
staxless dev up -s frontend
```

---

## staxless dev down

Stop all running services.

```bash
staxless dev down
```

---

## staxless dev logs

View service logs.

```bash
staxless dev logs [service]
```

### Options

| Option | Description |
|--------|-------------|
| `-f, --follow` | Stream logs in real-time |
| `-t, --tail <n>` | Show last n lines (default: 100) |

### Examples

```bash
# Follow all logs
staxless dev logs -f

# Show auth service logs
staxless dev logs auth-service

# Last 50 lines of stripe service
staxless dev logs stripe-service -t 50

# Follow specific service
staxless dev logs frontend -f
```

---

## staxless dev status

Show what's running.

```bash
staxless dev status
```

Output shows:
- Service name
- Status (running/stopped)
- Ports
- Health

---

## staxless dev reset

Full reset — stops everything, removes volumes, rebuilds.

```bash
staxless dev reset
```

**Warning:** This deletes local database data.

---

## Service Ports

| Service | Port |
|---------|------|
| Frontend | 3000 |
| Kong Gateway | 8000 |
| Kong Admin | 8002 |
| Auth | 8080 |
| User | 8082 |
| Stripe | 8083 |
| Email | 8084 |
| MongoDB | 27017 |

---

## Common Workflows

### Morning startup

```bash
staxless dev start
```

### Quick restart after code changes

```bash
# Hot reload handles most changes
# For dependency changes:
staxless dev down && staxless dev start
```

### Debug a specific service

```bash
# Watch its logs
staxless dev logs auth-service -f
```

### Fresh start

```bash
staxless dev reset
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
