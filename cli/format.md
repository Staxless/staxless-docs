# staxless format

> Format code with Prettier/Biome.

Ensures consistent code style across the project.

---

## Usage

```bash
staxless format [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--check` | Check formatting without changing files |
| `--path <dir>` | Format specific directory |

---

## Examples

### Format all files

```bash
staxless format
```

### Check without changing

```bash
staxless format --check
```

### Format specific directory

```bash
staxless format --path microservices/frontend/src
```

---

## What gets formatted

- `.ts`, `.tsx` — TypeScript files
- `.js`, `.jsx` — JavaScript files
- `.json` — JSON files
- `.css` — Stylesheets
- `.md` — Markdown files

---

## Configuration

Formatting rules are defined in:
- `.prettierrc` — Prettier configuration
- `biome.json` — Biome configuration

---

## Editor integration

### VS Code

Install the Prettier extension and enable format on save:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

### Pre-commit hook

Formatting runs automatically on commit via lint-staged.

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
