# Add Stripe Plan

> Set up pricing tiers with Stripe.

Staxless integrates with Stripe's product catalog. You define products in Stripe, and the checkout just works.

---

## How It Works

1. You create products and prices in Stripe Dashboard
2. Staxless fetches them automatically via `/products` endpoint
3. The checkout component displays your pricing
4. Users pay through Stripe's hosted checkout
5. Webhooks update subscription status

No need to hardcode prices in your frontend.

---

## 1. Create Products in Stripe

Go to [Stripe Dashboard](https://dashboard.stripe.com) → Products → Add Product.

Set up your tiers:

| Field | Example |
|-------|---------|
| Name | Pro Plan |
| Description | Everything you need |
| Price | $99/month |

Copy the Price ID (`price_xxx`) — you'll need it for the frontend config.

---

## 2. Configure the Sales Page

The sales page displays your pricing:

```typescript
// microservices/frontend/config/sales-config.ts
purchasePitch: {
  headline: 'Simple, transparent pricing',
  packages: [
    {
      name: 'Starter',
      description: 'Perfect for getting started',
      features: [
        'Feature one',
        'Feature two',
        'Email support',
      ],
      price: 29,
      ctaText: 'Get Started',
      productId: 'price_xxx_starter', // From Stripe
    },
    {
      name: 'Pro',
      description: 'For growing teams',
      features: [
        'Everything in Starter',
        'Priority support',
        'Advanced features',
      ],
      price: 99,
      originalPrice: 149,  // Shows strikethrough
      popular: true,        // "Most Popular" badge
      ctaText: 'Get Pro',
      productId: 'price_xxx_pro', // From Stripe
    },
  ],
}
```

The `productId` connects your UI to Stripe's product catalog.

---

## 3. Test Payments

Use Stripe test cards:

| Card | Result |
|------|--------|
| `4242 4242 4242 4242` | Success |
| `4000 0000 0000 0002` | Decline |
| `4000 0025 0000 3155` | 3D Secure |

Any future expiry, any CVC, any ZIP.

---

## Webhook Events

Stripe sends these events to your webhook endpoint:

| Event | What happens |
|-------|--------------|
| `checkout.session.completed` | Payment successful, subscription created |
| `customer.subscription.updated` | Plan changed |
| `customer.subscription.deleted` | Subscription cancelled |
| `invoice.payment_failed` | Payment failed |

The stripe-service handles all of this and updates user subscription status.

---

## Testing Webhooks Locally

```bash
stripe listen --forward-to localhost:8083/webhook
```

Copy the webhook secret to your env file.

---

## Going Live

1. Switch to live Stripe keys (`sk_live_...`)
2. Set up production webhook in Stripe Dashboard
3. Update `STRIPE_WEBHOOK_SECRET` with production secret

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
