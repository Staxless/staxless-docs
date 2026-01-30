# Staxless

> Ship your SaaS in days, while building on a scalable foundation.

Hey! These docs are designed to get out of your way. Find what you need, build what you want.

---

## What are you trying to do?

| I want to... | Go here |
|--------------|---------|
| Get running in 5 min | [Quick Start](./tutorials/quick-start.md) |
| Learn the dev workflow | [Development](./tutorials/development.md) |
| Learn CLI commands | [CLI Reference](./cli/README.md) |
| Add a page or feature | [Tutorials](./tutorials/README.md) |
| Create a new microservice | [Add a Service](./tutorials/add-service.md) |
| Understand auth/payments/email | [Features](./features/README.md) |
| Use pre-built components | [Components](./components/README.md) |
| Lock things down | [Security](./security/README.md) |
| Ship to production | [Deployment](./deployment/README.md) |

---

## The Stack

Next.js 15, React 19, Tailwind, Radix UI, Express, MongoDB, Kafka, Lucia Auth, Stripe, Mailgun, Kong, Docker Swarm.

Everything's in `microservices/`. Each service does one thing. Services communicate via Kafka events.

---

## The CLI

The Staxless CLI is your main interface. Here's what you'll use most:

```bash
# Setup
staxless init           # first-time setup wizard
staxless setup:frontend # customize branding
staxless setup:prod     # configure production secrets

# Development
staxless dev start      # spin up everything
staxless dev stop       # shut it down
staxless dev status     # check what's running
staxless dev logs       # view logs
staxless dev reset      # nuclear option - fresh start

# Production
staxless prod deploy    # ship it
staxless prod status    # check production
staxless prod logs      # view production logs

# Help
staxless help           # see all commands
staxless help <topic>   # deep dive on a topic
```

### Service Generator

Create new microservices instantly:

```bash
staxless generate service <service-name>

# Examples:
staxless generate service notification-service
staxless generate service analytics-service
```

Each generated service includes Express, MongoDB, Kafka, TypeScript, health checks, and error handling.

**[View full CLI documentation →](./cli/README.md)**

---

## Development Ports

When running locally, these are your endpoints:

| Service | Port | URL |
|---------|------|-----|
| Frontend | 3000 | http://localhost:3000 |
| Kong Gateway | 8000 | http://localhost:8000 |
| Kong Admin | 8002 | http://localhost:8002 |
| Auth | 8080 | http://localhost:8080 |
| User | 8082 | http://localhost:8082 |
| Stripe | 8083 | http://localhost:8083 |
| Email | 8084 | http://localhost:8084 |
| MongoDB | 27017 | mongodb://localhost:27017 |

All API requests go through Kong at `:8000`. The individual service ports are exposed for debugging.

---

## Stripe Webhooks (Dev)

Stripe needs to reach your local machine. Use the Stripe CLI:

```bash
brew install stripe/stripe-cli/stripe
stripe login
stripe listen --forward-to localhost:8083/webhook
```

Copy the webhook secret it prints and add to your env file as `STRIPE_WEBHOOK_SECRET`.

---

That's it. Go build something.

---

