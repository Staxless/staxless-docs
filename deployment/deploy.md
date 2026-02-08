# Deploy Guide

> Get your Staxless app live.

---

## Before You Start

Check these boxes:

- [ ] GitHub Secrets configured
- [ ] Stripe webhooks point to production domain
- [ ] OAuth redirect URIs updated
- [ ] Domain and DNS configured
- [ ] Cloudflare tunnel set up
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
| OAuth | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET` |
| Payments | `STRIPE_PRIVATE_KEY`, `STRIPE_WEBHOOK_SECRET` |
| Email | `MAILGUN_API_KEY` |
| Cloudflare | `CLOUDFLARE_TUNNEL_TOKEN` |

---

## Step 2: Deploy

**Auto (recommended):**

```bash
git push origin main
```

GitHub Actions builds images, pushes to registry, deploys to Swarm.

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

Secure ingress without exposing ports:

1. Create tunnel in Cloudflare dashboard
2. Copy the token
3. Add as `CLOUDFLARE_TUNNEL_TOKEN`

Runs as a service in Swarm.

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
