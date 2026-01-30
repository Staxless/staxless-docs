# staxless status

> Show current project configuration status.

Quick overview of your project state.

---

## Usage

```bash
staxless status [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--json` | Output as JSON |

---

## Output

```
Staxless Project Status
=======================

Project:     my-saas-app
Version:     1.0.0
Environment: development

Services:
  ✓ frontend        running   :3000
  ✓ auth-service    running   :8080
  ✓ user-service    running   :8082
  ✓ stripe-service  running   :8083
  ✗ email-service   stopped

Configuration:
  ✓ Environment files configured
  ✓ Docker compose valid
  ✓ Dependencies installed
```

---

## Examples

### Standard output

```bash
staxless status
```

### JSON output (for scripts)

```bash
staxless status --json
```

```json
{
  "project": "my-saas-app",
  "version": "1.0.0",
  "environment": "development",
  "services": {
    "frontend": { "status": "running", "port": 3000 },
    "auth-service": { "status": "running", "port": 8080 }
  }
}
```

### Use in scripts

```bash
# Check if all services running
if staxless status --json | jq -e '.services | all(.status == "running")'; then
  echo "All services healthy"
fi
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
