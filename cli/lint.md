# staxless lint

> Run the linter across your codebase.

Uses ESLint with the project's configuration.

---

## Usage

```bash
staxless lint [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--fix` | Automatically fix problems |
| `--path <dir>` | Lint specific directory |

---

## Examples

### Check for issues

```bash
staxless lint
```

### Auto-fix issues

```bash
staxless lint --fix
```

### Lint specific directory

```bash
staxless lint --path microservices/auth-service
```

---

## Configuration

Linting rules are defined in:
- `.eslintrc.js` — ESLint configuration
- `biome.json` — Biome configuration (if using)

---

## Common issues

### Unused variables

```
error: 'foo' is defined but never used
```

**Fix:** Remove or use the variable, or prefix with `_`:

```typescript
const _unusedButIntentional = getValue();
```

### Missing imports

```
error: 'useState' is not defined
```

**Fix:** Add the import:

```typescript
import { useState } from 'react';
```

---

## CI Integration

Add to your GitHub workflow:

```yaml
- name: Lint
  run: staxless lint
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
