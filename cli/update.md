# staxless update

> Check for and install CLI updates.

Keep your CLI up to date.

---

## Usage

```bash
staxless update [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--check` | Check for updates without installing |

---

## Examples

### Update to latest

```bash
staxless update
```

### Check only

```bash
staxless update --check
```

Output:

```
Current version: 2.0.0
Latest version:  2.1.0

Run 'staxless update' to install.
```

---

## Manual update

If the update command fails:

```bash
npm install -g @staxless/cli@latest
```

---

## Version info

```bash
staxless --version
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
