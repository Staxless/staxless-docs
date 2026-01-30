# staxless doctor

> Check system prerequisites and configuration.

Run this when something isn't working.

---

## Usage

```bash
staxless doctor [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--fix` | Attempt to fix issues automatically |

---

## What it checks

### System requirements
- Node.js version (18+)
- npm version
- Docker installed and running
- Docker Compose version
- Git version

### Project configuration
- Package.json exists
- Dependencies installed
- Environment files present
- Docker compose files valid

### Services
- MongoDB connectivity
- Kafka connectivity (if enabled)
- Service health endpoints

---

## Examples

### Run diagnostics

```bash
staxless doctor
```

Output:

```
Checking prerequisites...

✓ Node.js      v20.10.0
✓ npm          v10.2.0
✓ Docker       v24.0.7
✓ Compose      v2.23.0
✓ Git          v2.42.0

Checking configuration...

✓ package.json found
✓ Dependencies installed
✓ Environment configured
✗ Missing: microservices/auth-service/secrets/.env

1 issue found
```

### Auto-fix issues

```bash
staxless doctor --fix
```

This will:
- Copy `.env.example` to `.env` where missing
- Install missing dependencies
- Create missing directories

---

## Common issues

### Docker not running

```
✗ Docker    Not running
```

**Fix:** Start Docker Desktop or the Docker daemon.

### Missing environment file

```
✗ Missing: microservices/auth-service/secrets/.env
```

**Fix:**

```bash
cd microservices/auth-service
cp secrets/.env.example secrets/.env
```

Or run with `--fix`.

### Old Node.js version

```
✗ Node.js   v16.0.0 (requires 18+)
```

**Fix:** Update Node.js using nvm:

```bash
nvm install 20
nvm use 20
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
