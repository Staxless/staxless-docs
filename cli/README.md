# Staxless CLI

> Your command center for building, running, and deploying Staxless apps.

---

## Installation

```bash
npm install -g @staxless/cli
```

Verify installation:

```bash
staxless --version
```

---

## Quick Reference

| Command | What it does |
|---------|--------------|
| `staxless init` | First-time project setup |
| `staxless dev start` | Start all services locally |
| `staxless dev down` | Stop all services |
| `staxless generate service <name>` | Create a new microservice |
| `staxless prod deploy` | Deploy to production |
| `staxless doctor` | Check your setup |

---

## Command Groups

### Setup
- [init](./init.md) — First-time setup wizard
- [setup:frontend](./setup-frontend.md) — Configure branding
- [setup:prod](./setup-prod.md) — Configure production secrets

### Development
- [dev](./dev.md) — Start, stop, logs, reset local environment

### Production
- [prod](./prod.md) — Deploy and manage production

### Generators
- [generate](./generate.md) — Scaffold new services and resources

### Code Quality
- [lint](./lint.md) — Run linter
- [format](./format.md) — Format code
- [test](./test.md) — Run tests

### Utilities
- [doctor](./doctor.md) — Check prerequisites
- [status](./status.md) — Show project status
- [update](./update.md) — Update CLI
- [help](./help.md) — Get help on any topic

---

## Global Options

These work with any command:

```bash
staxless <command> [options]
```

| Option | Description |
|--------|-------------|
| `--verbose` | Show detailed output |
| `--debug` | Show debug-level logging |
| `--dry-run` | Preview changes without executing |
| `--non-interactive` | Skip prompts, use defaults |
| `-c, --config <path>` | Use specific config file |
| `-v, --version` | Show CLI version |
| `-h, --help` | Show help |

---

## Common Workflows

### Starting a new project

```bash
# 1. Initialize (first time only)
staxless init

# 2. Start development
staxless dev start

# 3. Open http://localhost:3000
```

### Daily development

```bash
# Start services
staxless dev start

# Check what's running
staxless dev status

# View logs
staxless dev logs -f

# Stop when done
staxless dev down
```

### Adding a new microservice

```bash
# Generate the service
staxless generate service payment-service

# Navigate and install
cd microservices/payment-service
npm install

# Start development
npm run dev
```

### Deploying to production

```bash
# Set up secrets (first time)
staxless setup:prod

# Deploy
staxless prod deploy

# Check status
staxless prod status
```

---

## Configuration File

Create `.staxless.json` in your project root for defaults:

```json
{
  "defaults": {
    "frontend": {
      "directory": "./microservices/frontend"
    },
    "docker": {
      "composeFile": "./docker/compose/docker-compose.dev.yml"
    }
  }
}
```

Use with `--config`:

```bash
staxless dev start --config .staxless.json
```

---

## Environment Variables

| Variable | Description |
|----------|-------------|
| `STAXLESS_LICENSE_KEY` | Your license key |
| `GITHUB_TOKEN` | GitHub personal access token |
| `STAXLESS_CONFIG` | Default config file path |

---

## Troubleshooting

### Command not found

```bash
# Reinstall globally
npm install -g @staxless/cli

# Or use npx
npx @staxless/cli dev start
```

### Permission errors

```bash
# Fix npm permissions
sudo chown -R $(whoami) ~/.npm
```

### Docker issues

```bash
# Check Docker is running
docker info

# Run diagnostics
staxless doctor --fix
```

---

<p align="center">
  <a href="../README.md">← Staxless Docs</a>
</p>
