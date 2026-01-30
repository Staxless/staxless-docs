# Development Workflow

> Your day-to-day when building with Staxless.

---

## Starting Up

```bash
staxless dev start
```

This spins up all services via Docker Compose. First run takes a bit while images build.

---

## Development Ports

Everything's exposed locally for debugging:

| Service | Port | What it does |
|---------|------|--------------|
| Frontend | 3000 | Your Next.js app |
| Kong Gateway | 8000 | API gateway (all requests go here) |
| Kong Admin | 8002 | Kong dashboard |
| Auth | 8080 | Authentication service |
| User | 8082 | User management |
| Stripe | 8083 | Payment processing |
| Email | 8084 | Email delivery |
| MongoDB | 27017 | Database |

In production, only Kong is exposed. Everything else is internal.

---

## Watching Logs

```bash
staxless dev logs           # all services
staxless dev logs frontend  # just frontend
staxless dev logs -f        # follow mode
```

---

## Hot Reload

Frontend hot reloads automatically. Backend services need a restart:

```bash
staxless dev down && staxless dev start
```

Or restart a single service:

```bash
docker compose restart auth-service
```

---

## Stripe Webhooks

Stripe can't reach localhost, so you need to forward webhooks:

```bash
brew install stripe/stripe-cli/stripe
stripe login
stripe listen --forward-to localhost:8083/webhook
```

It'll print a webhook secret. Add it to your env:

```bash
STRIPE_WEBHOOK_SECRET=whsec_xxx
```

Leave this running in a terminal while developing payment flows.

---

## Database Access

Connect directly to MongoDB:

```bash
mongosh mongodb://localhost:27017/staxless-cluster
```

Or use MongoDB Compass at `mongodb://localhost:27017`.

---

## Checking Health

```bash
staxless dev status
```

Or hit health endpoints:

```bash
curl http://localhost:8080/health  # Auth
curl http://localhost:8082/health  # User
curl http://localhost:8083/health  # Stripe
curl http://localhost:8084/health  # Email
```

---

## Common Commands

```bash
staxless dev start      # start everything
staxless dev stop       # stop everything
staxless dev status     # check status
staxless dev logs       # view logs
staxless dev reset      # full reset (clears volumes)
```

---

## Environment Files

Dev config lives in `environments/dev/staxless.dev.env`.

Never commit real credentials. The template shows what's needed.

---

## Debugging Tips

**Service won't start?**
```bash
staxless dev logs <service-name>
```

**Need a fresh start?**
```bash
staxless dev reset
```

**Port conflict?**
```bash
docker stop $(docker ps -q)
```

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
