# staxless setup:frontend

> Configure frontend branding and styling.

Aliases: `staxless setup-frontend`

---

## Usage

```bash
staxless setup:frontend [options]
```

## Options

| Option | Description |
|--------|-------------|
| `-d, --directory <path>` | Frontend directory path |
| `--dry-run` | Preview changes |

---

## What it configures

- **Brand name** — Your app name
- **Colors** — Primary, secondary, accent colors
- **Logo** — Upload or generate
- **Fonts** — Google Fonts or custom
- **Social links** — Twitter, GitHub, etc.

---

## Examples

### Interactive setup

```bash
staxless setup:frontend
```

### Specify frontend path

```bash
staxless setup:frontend -d ./apps/web
```

---

## Configuration file

Settings are saved to `microservices/frontend/src/config/brand.ts`:

```typescript
export const brand = {
  name: 'My App',
  tagline: 'Build faster',
  colors: {
    primary: '#6366f1',
    secondary: '#8b5cf6',
  },
  // ...
};
```

---

## After setup

Changes take effect immediately with hot reload.

To rebuild:

```bash
staxless dev start -s frontend
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
