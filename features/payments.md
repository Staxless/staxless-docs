# Payments

> Stripe integration for subscriptions and one-time payments.

Dig into this when setting up pricing or debugging checkout.

---

## The Flow

```
User selects plan
     ↓
POST /checkout
     ↓
Stripe Service creates session
     ↓
Redirect to Stripe Checkout
     ↓
User pays
     ↓
Stripe webhook → /webhook
     ↓
Subscription updated
     ↓
Redirect to /checkout/success
```

---

## Key Files

| File | What it does |
|------|--------------|
| `stripe-service/src/routes/stripe-routes.ts` | Checkout endpoints |
| `stripe-service/src/routes/stripe-webhook-routes.ts` | Webhook handler |
| `frontend/config/sales-config.ts` | Pricing packages |
| `frontend/src/app/(protected)/checkout/` | Checkout UI |

---

## Environment

```bash
# Development (test mode)
STRIPE_PRIVATE_KEY=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Production (live mode)
STRIPE_PRIVATE_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
```

---

## Testing Webhooks Locally

```bash
brew install stripe/stripe-cli/stripe
stripe login
stripe listen --forward-to localhost:8083/webhook
```

Copy the signing secret it gives you to your env file.

---

## Webhook Events Handled

- `checkout.session.completed` — Payment successful
- `customer.subscription.created` — New subscription
- `customer.subscription.updated` — Subscription changed
- `customer.subscription.deleted` — Subscription cancelled

---

## API Endpoints

| Endpoint | Method | Auth | What it does |
|----------|--------|------|--------------|
| `/products` | GET | No | List Stripe products |
| `/checkout` | POST | Yes | Create checkout session |
| `/checkout/session/:id` | GET | Yes | Get session details |
| `/webhook` | POST | No | Receive Stripe events |

---

<p align="center">
  <a href="./README.md">← Staxless Features</a>
</p>
