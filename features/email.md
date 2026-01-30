# Email

> Mailgun-powered transactional emails.

For magic links and notifications.

---

## Configuration

```bash
# environments/dev/staxless.dev.env
MAILGUN_API_KEY=key-xxx
MAILGUN_DOMAIN=mg.yourdomain.com  # or sandbox for testing
MAILGUN_FROM_NAME=YourSaaS
MAILGUN_FROM_EMAIL=noreply@yourdomain.com
```

---

## Key Files

| File | What it does |
|------|--------------|
| `email-service/src/server.ts` | Service entry |
| `email-service/src/config/mailgun-config.ts` | Mailgun setup |
| `email-service/src/consumers/` | Kafka handlers |

---

## Sending via Kafka

The email service listens to Kafka events:

```typescript
await kafkaProducer.send({
  topic: 'email-events',
  messages: [{
    value: JSON.stringify({
      type: 'SEND_EMAIL',
      payload: {
        to: 'user@example.com',
        subject: 'Welcome!',
        html: '<h1>Welcome to our platform</h1>',
      }
    })
  }]
});
```

---

## Direct API

```bash
POST /email/send
Content-Type: application/json

{
  "to": "user@example.com",
  "subject": "Your Subject",
  "html": "<h1>Email Content</h1>"
}
```

---

## Testing with Sandbox

Mailgun gives you a sandbox domain for testing:

1. Use `sandbox*.mailgun.org` as domain
2. Add authorized recipients in Mailgun dashboard
3. Only those emails will receive messages

---

## Going to Production

1. Add and verify your domain in Mailgun
2. Configure DNS records (SPF, DKIM, MX)
3. Update env vars with production domain
4. Remove sandbox restrictions

---

<p align="center">
  <a href="./README.md">← Staxless Features</a>
</p>
