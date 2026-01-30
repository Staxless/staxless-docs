# Staxless Features

> The core systems that power your SaaS.

Only dig into these when you need to customize or debug. They work out of the box.

---

| Feature | When you'd need this |
|---------|---------------------|
| [Authentication](./authentication.md) | Customizing login, adding OAuth providers, debugging sessions |
| [Payments](./payments.md) | Stripe setup, checkout flow, webhooks |
| [Email](./email.md) | Magic links, transactional emails, Mailgun config |

---

## The Architecture

Each feature runs as its own microservice. Here's why that matters:

```
Frontend (Next.js)
       │
Kong Gateway ← Single entry point, handles auth, rate limiting
       │
       ├── Auth Service ──┐
       ├── User Service ──┼── MongoDB (shared)
       ├── Stripe Service─┤
       └── Email Service ─┴── Kafka (event bus)
```

**Why microservices?**
- Scale what needs scaling. Payment processing getting hammered? Scale just that service.
- Deploy independently. Update auth without touching payments.
- Fail gracefully. If email goes down, users can still log in.
- Clear boundaries. Each service owns its domain.

---

## Kafka Integration

Services communicate through Kafka events. This keeps things decoupled.

**Example flow — Magic Link Login:**
1. Auth service receives login request
2. Auth publishes `SEND_MAGIC_LINK` event to Kafka
3. Email service consumes event, sends email
4. Auth service doesn't wait or care if email succeeds

```typescript
// Publishing an event
await kafkaProducer.send({
  topic: 'email-events',
  messages: [{
    value: JSON.stringify({
      type: 'SEND_MAGIC_LINK',
      payload: { to: 'user@example.com', token: '...' }
    })
  }]
});
```

**Available topics:**
- `email-events` — Email delivery requests
- `auth-events` — Auth-triggered actions
- `user-events` — User lifecycle events
- `payment-events` — Payment notifications

---

## Database

All services share a MongoDB instance but use separate collections. Each service owns its data.

| Service | Collections |
|---------|-------------|
| Auth | `sessions`, `oauth_accounts` |
| User | `users`, `profiles` |
| Stripe | `subscriptions`, `payment_history` |

Services never directly access another service's collections. They communicate through the API or Kafka.

---

## Adding Your Own

Want to add a new feature service? See [Add a New Service](../tutorials/add-service.md).

The pattern is simple:
1. Create the service in `microservices/`
2. Add routes and models
3. Register in Kong
4. Hook into Kafka if needed
5. Add to Docker Compose

---

<p align="center">
  <a href="../README.md">← Staxless Docs</a>
</p>
