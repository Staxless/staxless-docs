# staxless setup:prod

> Configure GitHub secrets for production deployment.

Aliases: `staxless setup-prod`

---

## Usage

```bash
staxless setup:prod [options]
```

## Options

| Option | Description |
|--------|-------------|
| `--from-env <file>` | Read secrets from env file (default: `prod.env`) |
| `--check` | Verify secrets without setting |
| `--dry-run` | Preview what would be set |

---

## Required secrets

The command configures these GitHub secrets:

| Secret | Description |
|--------|-------------|
| `DATABASE_URL` | MongoDB connection string |
| `STRIPE_PRIVATE_KEY` | Stripe secret key |
| `STRIPE_WEBHOOK_SECRET` | Stripe webhook signing secret |
| `MAILGUN_API_KEY` | Mailgun API key |
| `GITHUB_CLIENT_ID` | GitHub OAuth app ID |
| `GITHUB_CLIENT_SECRET` | GitHub OAuth secret |
| `GOOGLE_CLIENT_ID` | Google OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | Google OAuth secret |

---

## Examples

### Interactive setup

```bash
staxless setup:prod
```

### From env file

Create `prod.env`:

```bash
DATABASE_URL=mongodb+srv://user:pass@cluster.mongodb.net/db
STRIPE_PRIVATE_KEY=sk_live_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx
MAILGUN_API_KEY=key-xxx
```

Then:

```bash
staxless setup:prod --from-env prod.env
```

### Check existing secrets

```bash
staxless setup:prod --check
```

---

## Prerequisites

1. **GitHub CLI** must be installed and authenticated:

```bash
gh auth login
```

2. **Repository access** — You need write access to the repo.

---

## Security notes

- Secrets are encrypted by GitHub
- Never commit `prod.env` to git
- Add `prod.env` to `.gitignore`
- Rotate secrets periodically

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
