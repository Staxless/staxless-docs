# Quick Start

> Get Staxless running in 5 minutes.

Let's get you up and running. Should be quick.

---

## You'll Need

- Node 18+
- Docker
- GitHub CLI (`gh`)

Don't have these? `brew install node docker gh` on Mac.

---

## Option A: The CLI (Recommended)

```bash
npm install -g @staxless/cli
staxless init
```

The wizard handles everything — license, cloning, OAuth setup, Stripe, dependencies, env files.

When it's done:

```bash
staxless dev start
```

Open http://localhost:3000. That's it.

---

## Option B: Manual Setup

```bash
git clone https://github.com/Staxless/staxless.git my-saas
cd my-saas
npm run install:microservices
cp environments/dev/staxless.dev.env.template environments/dev/staxless.dev.env
```

Edit `environments/dev/staxless.dev.env` with your credentials:

```bash
GOOGLE_CLIENT_ID=your_id
GOOGLE_CLIENT_SECRET=your_secret
STRIPE_PRIVATE_KEY=sk_test_...
MAILGUN_API_KEY=key-...
```

Then:

```bash
npm run dev:start
```

---

## Check It's Working

```bash
staxless dev status
```

Or hit the health endpoints:

```bash
curl http://localhost:8080/health  # Auth
curl http://localhost:8082/health  # User
curl http://localhost:8083/health  # Stripe
```

---

## Something Wrong?

| Problem | Fix |
|---------|-----|
| `ECONNREFUSED` on 27017 | `staxless dev reset` |
| OAuth redirect mismatch | Check `GOOGLE_REDIRECT_URI` is `http://localhost:8000/oauth/google/callback` |
| Port already in use | `docker stop $(docker ps -q)` |

---

## Next Up

- [Development Workflow](./development.md) — Learn the day-to-day commands
- [Customize your branding](./customize-brand.md)
- [Add your first page](./add-page.md)

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
