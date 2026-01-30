# Configuration Reference

> All the environment variables and where they go.

---

## Development

File: `environments/dev/staxless.dev.env`

```bash
# App
NODE_ENV=development

# Database
DATABASE_URI=mongodb://mongodb-service:27017
DATABASE_NAME=staxless-cluster

# URLs
API_GATEWAY_URL=http://localhost:8000
FRONTEND_URL=http://localhost:3000
COOKIE_DOMAIN=localhost

# OAuth - Google
GOOGLE_CLIENT_ID=xxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxx
GOOGLE_REDIRECT_URI=http://localhost:8000/oauth/google/callback

# OAuth - GitHub
GITHUB_CLIENT_ID=xxx
GITHUB_CLIENT_SECRET=xxx

# Stripe
STRIPE_PRIVATE_KEY=sk_test_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx

# Mailgun
MAILGUN_API_KEY=key-xxx
MAILGUN_DOMAIN=sandbox.mailgun.org
MAILGUN_FROM_NAME=YourSaaS
MAILGUN_FROM_EMAIL=noreply@yourdomain.com

# Optional
PAYLOAD_SECRET=xxx
```

---

## Production

Set via `staxless setup:prod` which adds them as GitHub Secrets.

| Secret | What it's for |
|--------|---------------|
| `DIGITALOCEAN_TOKEN` | Infrastructure provisioning |
| `DO_MANAGER_IP` | Swarm manager address |
| `GOOGLE_CLIENT_ID` | Google OAuth |
| `GOOGLE_CLIENT_SECRET` | Google OAuth |
| `GOOGLE_REDIRECT_URI` | Must be production URL |
| `GITHUB_CLIENT_ID` | GitHub OAuth |
| `GITHUB_CLIENT_SECRET` | GitHub OAuth |
| `STRIPE_PRIVATE_KEY` | Live key (`sk_live_...`) |
| `STRIPE_WEBHOOK_SECRET` | Production webhook secret |
| `MAILGUN_API_KEY` | Email delivery |
| `CLOUDFLARE_TUNNEL_TOKEN` | Secure ingress |
| `PAYLOAD_SECRET` | CMS encryption |

---

## Brand Config

File: `microservices/frontend/config/brand-config.ts`

```typescript
export const COMPANY_CONFIG = {
  name: 'YourSaaSName',
  tagline: 'Your Tagline',
  description: 'What you do',
  website: 'https://yourdomain.com',
  email: 'support@yourdomain.com',
};

export const CUSTOM_STYLE: BrandStyle = {
  colors: {
    primary: '#6366F1',
    secondary: '#8B5CF6',
    background: '#0F172A',
    textPrimary: '#F8FAFC',
  },
};

export const SOCIAL_CONFIG = {
  twitter: '@YourHandle',
  github: 'https://github.com/you',
  linkedin: 'https://linkedin.com/company/you',
};
```

---

## Kong Rate Limits

File: `microservices/kong-gateway/src/kong.yaml`

| Service | Per Minute | Per Hour |
|---------|------------|----------|
| Auth | 100 | 1,000 |
| Stripe | 50 | 500 |
| Email | 30 | 300 |
| User | 60 | 600 |

---

## Adding Protected Routes

```yaml
# kong-gateway/src/kong.yaml
routes:
  - name: your-route
    methods: [GET, POST]
    paths: [/your/path]
    plugins:
      - name: rbac-auth
        config:
          auth_service_url: http://auth-service:8080
          required_roles: [user]
```

---

## Service Ports

| Service | Port |
|---------|------|
| Frontend | 3000 |
| Kong Gateway | 8000 |
| Kong Admin | 8002 |
| Auth | 8080 |
| User | 8082 |
| Stripe | 8083 |
| Email | 8084 |
| MongoDB | 27017 |
| Kafka | 9092 |

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
