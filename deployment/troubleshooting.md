# Troubleshooting

> Something's broken? Let's fix it.

---

## Quick Diagnostics

```bash
staxless doctor        # check prerequisites
staxless dev status    # check services
staxless dev logs      # view logs
staxless dev reset     # nuclear option
```

---

## Common Issues

### Services Won't Start

```bash
staxless dev reset
```

If that doesn't work:

```bash
docker compose -f docker/compose/docker-compose.dev.yml down -v
docker compose -f docker/compose/docker-compose.dev.yml up --build
```

Also check Docker has enough memory (4GB+) and disk space.

---

### MongoDB Connection Errors

`ECONNREFUSED` on port 27017?

```bash
docker ps | grep mongo
staxless dev logs mongodb-service
docker compose restart mongodb-service
```

If MongoDB's data is corrupted:

```bash
docker volume rm staxless_dev_mongo_data
staxless dev start
```

---

### Kafka Warnings

"Kafka connection failed" at startup is normal. Kafka takes time to initialize. Auth service retries automatically.

If it never connects:

```bash
docker ps | grep kafka
staxless dev logs kafka-service
docker compose restart zookeeper kafka-service
```

---

### OAuth Redirect Mismatch

Check your OAuth app settings:

- **Dev:** `http://localhost:8000/oauth/google/callback`
- **Prod:** `https://api.yourdomain.com/oauth/google/callback`

Update env and restart:

```bash
staxless dev down && staxless dev start
```

---

### Port Already in Use

```bash
lsof -i :3000
kill -9 <PID>
```

Or stop everything:

```bash
docker stop $(docker ps -q)
```

---

### Hot Reload Not Working

```bash
docker compose restart frontend
```

Check volume mounts and file permissions.

---

### Session Issues

User getting logged out? Check:

- Cookie name: `auth_session`
- Domain: `localhost` (dev) or your domain (prod)
- Secure: false (dev), true (prod)

Clear browser cookies and try again.

```bash
staxless dev logs auth-service
```

---

### Stripe Webhooks Not Working

Payments complete but subscription doesn't update?

```bash
stripe listen --forward-to localhost:8083/webhook
```

Copy the webhook secret to your env. Check logs:

```bash
staxless dev logs stripe-service
```

---

### Emails Not Sending

```bash
staxless dev logs email-service
```

Mailgun sandbox only sends to verified recipients. Add test emails in Mailgun dashboard.

---

### Build Failures

npm issues:

```bash
npm cache clean --force
rm -rf microservices/*/node_modules
npm run install:microservices
```

TypeScript errors:

```bash
cd microservices/frontend && npm run typecheck
```

Out of disk:

```bash
docker system prune -a
```

---

## Reset Everything

When nothing else works:

```bash
staxless dev down
docker system prune -a --volumes
find . -name "node_modules" -type d -prune -exec rm -rf {} +
npm run install:microservices
staxless dev start
```

---

## Getting Help

```bash
staxless help
staxless --debug dev start
```

---

<p align="center">
  <a href="./README.md">← Staxless Deployment</a>
</p>
