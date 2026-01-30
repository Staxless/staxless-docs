# staxless prod

> Manage production deployments.

Wraps Docker Swarm commands for production infrastructure.

---

## Commands

| Command | Description |
|---------|-------------|
| `staxless prod deploy` | Deploy to production |
| `staxless prod status` | Check deployment status |
| `staxless prod logs` | View production logs |
| `staxless prod remove` | Take down production stack |

---

## staxless prod deploy

Deploy the stack to production.

```bash
staxless prod deploy
```

### What it does

1. Validates production configuration
2. Pushes images to registry
3. Deploys Docker Swarm stack
4. Waits for services to be healthy

---

## staxless prod status

Check what's running in production.

```bash
staxless prod status
```

Shows:
- Service replicas
- Current state
- Last update time

---

## staxless prod logs

View production logs.

```bash
staxless prod logs [service]
```

### Options

| Option | Description |
|--------|-------------|
| `-f, --follow` | Stream logs |
| `--since <duration>` | Logs since (e.g., "1h", "30m") |

### Examples

```bash
# All logs
staxless prod logs

# Follow auth service
staxless prod logs auth-service -f

# Last hour
staxless prod logs --since 1h
```

---

## staxless prod remove

Take down the production stack.

```bash
staxless prod remove
```

**Warning:** This stops all production services.

---

## Before First Deploy

1. **Set up secrets**

```bash
staxless setup:prod
```

2. **Configure your server**

Ensure Docker Swarm is initialized:

```bash
docker swarm init
```

3. **Set environment variables**

```bash
export DOCKER_HOST=ssh://user@your-server
```

---

## Deployment Checklist

```bash
# 1. Run tests
staxless test

# 2. Check linting
staxless lint

# 3. Verify secrets are set
staxless setup:prod --check

# 4. Deploy
staxless prod deploy

# 5. Verify
staxless prod status
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
