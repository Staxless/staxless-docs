# staxless init

> First-time project setup wizard.

Sets up your development environment, installs dependencies, and configures services.

---

## Usage

```bash
staxless init [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--skip-deps` | Skip installing npm dependencies |
| `--skip-linter` | Skip linter configuration |
| `--dry-run` | Preview what would happen |

---

## What it does

1. **Validates prerequisites** — Checks Node.js, Docker, Git versions
2. **Authenticates** — Verifies your license key
3. **Installs dependencies** — Runs `npm install` in all services
4. **Configures environment** — Sets up `.env` files from templates
5. **Sets up linter** — Configures ESLint/Prettier

---

## Examples

### Full setup

```bash
staxless init
```

### Skip dependency installation

```bash
staxless init --skip-deps
```

### Preview only

```bash
staxless init --dry-run
```

---

## After init

```bash
# Start development
staxless dev start

# Open the app
open http://localhost:3000
```

---

## Troubleshooting

### "Prerequisites not met"

Run the doctor to see what's missing:

```bash
staxless doctor
```

### "License invalid"

Check your license key:

```bash
export STAXLESS_LICENSE_KEY=your-key-here
staxless init
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
