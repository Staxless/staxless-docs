# Deploy Guide

> Get your Staxless app live.

---

## Before You Start

Check these boxes:

- [ ] GitHub Secrets configured
- [ ] Stripe webhooks point to production domain
- [ ] OAuth redirect URIs updated
- [ ] Domain and DNS configured (automated if Cloudflare secrets are set)
- [ ] Cloudflare API token and Account ID configured
- [ ] MongoDB Atlas cluster ready

---

## Step 1: Set Up Secrets

```bash
staxless setup:prod
```

This wizard sets everything up. Or import from a file:

```bash
staxless setup:prod --from-file .env.production
```

What you'll need:

| Category | Secrets |
|----------|---------|
| Infrastructure | `DIGITALOCEAN_TOKEN`, `DO_SPACES_ACCESS_KEY`, `DO_SPACES_SECRET_KEY`, `SSH_PRIVATE_KEY`, `SSH_FINGERPRINT`, `MANAGER_IP` |
| Registry | `GHCR_TOKEN` (optional — only if your org restricts GitHub Actions package creation) |
| OAuth | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GH_OAUTH_CLIENT_ID`, `GH_OAUTH_CLIENT_SECRET` |
| Payments | `STRIPE_PRIVATE_KEY`, `STRIPE_WEBHOOK_SECRET` |
| Email | `MAILGUN_API_KEY` |
| Cloudflare | `CLOUDFLARE_API_TOKEN`, `CLOUDFLARE_ACCOUNT_ID` |

---

## Step 2: Deploy

**Auto (recommended):**

```bash
git push origin main
```

GitHub Actions builds images, pushes to registry, deploys to Swarm.

> **GHCR 403?** If image pushes fail with "403 Forbidden", your org restricts Actions package creation. Add a `GHCR_TOKEN` secret (classic PAT with `write:packages` scope) via `staxless setup:prod`.

**Manual:**

```bash
staxless prod deploy
```

---

## Deployment Actions

Triggered via `.github/workflows/deploy.yml` (uses [`staxless/staxless-deploy`](https://github.com/staxless/staxless-deploy)):

| Action | When to use |
|--------|-------------|
| `initial-deploy` | First time infrastructure + services |
| `update-services` | Rolling updates with automatic rollback |
| `add-service` | Adding a new microservice |
| `destroy` | Graceful shutdown + tear down infrastructure |

---

## Infrastructure

Infrastructure is managed by Terraform inside `staxless/staxless-deploy`. The `initial-deploy` action provisions VPC, droplets, firewall, and Swarm cluster on DigitalOcean automatically.

Configuration can be customized via `.staxless.yml` in your project root or by passing inputs to the reusable workflows.

---

## After Deploy

Check it worked:

```bash
staxless prod status
staxless prod logs frontend
```

Hit the health endpoints:

```bash
curl https://yourdomain.com/api/health
```

---

## Rollback

If something's wrong:

```bash
docker service update --rollback staxless_frontend
```

Or revert and redeploy:

```bash
git revert HEAD
git push origin main
```

---

## Cloudflare Tunnel

Secure ingress without exposing ports — **fully automated** during initial deploy.

**What you provide:**
- `CLOUDFLARE_API_TOKEN` — Custom token with Account:Cloudflare Tunnel:Edit, Zone:DNS:Edit, Zone:Zone:Read permissions
- `CLOUDFLARE_ACCOUNT_ID` — Found in the Cloudflare dashboard sidebar

**What the automation does:**
1. Creates a Cloudflare Tunnel (or reuses existing)
2. Configures DNS records for `domain.com` and `api.domain.com`
3. Sets tunnel ingress rules (frontend + Kong API Gateway)
4. Stores the `CLOUDFLARE_TUNNEL_TOKEN` as a Docker secret and GitHub repo secret

Runs as a service in Swarm. The entire flow is idempotent — safe to re-run.

---

## MongoDB Atlas

1. Create cluster at [MongoDB Atlas](https://cloud.mongodb.com)
2. Create database user
3. Whitelist your IPs
4. Get connection string
5. Add as `DATABASE_URL`

---

## Monitoring

```bash
staxless prod logs -f
```

Recommended tools:
- **Uptime:** UptimeRobot, Pingdom
- **Errors:** Sentry
- **Logs:** Papertrail, Logtail

---

<p align="center">
  <a href="./README.md">← Staxless Deployment</a>
</p>
