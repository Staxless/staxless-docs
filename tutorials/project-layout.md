# Project Layout

> Where things live in a Staxless project.

You'll mostly work in `microservices/`.

---

## The Folder Structure

```
staxless/
├── microservices/
│   ├── frontend/           # Next.js (port 3000)
│   │   ├── src/app/       # Pages
│   │   ├── src/components/ # UI
│   │   └── config/        # Branding, sales
│   ├── auth-service/       # Auth (port 8080)
│   ├── user-service/       # Users (port 8082)
│   ├── stripe-service/     # Payments (port 8083)
│   ├── email-service/      # Email (port 8084)
│   └── kong-gateway/       # API Gateway (port 8000)
├── docker/compose/         # Docker configs
├── environments/           # Env files
└── .infrastructure/        # Terraform (production)
```

---

## Quick Navigation

| I want to... | Go to |
|--------------|-------|
| Add a page | `frontend/src/app/[route]/page.tsx` |
| Add an API | `[service]/src/routes/` |
| Change branding | `frontend/config/brand-config.ts` |
| Add a model | `[service]/src/models/` |
| Configure routing | `kong-gateway/src/kong.yaml` |

---

## How It Fits Together

```
Frontend (3000)
     │
Kong Gateway (8000)  ← CORS, rate limiting, auth
     │
     ├── Auth Service (8080)
     ├── User Service (8082)
     ├── Stripe Service (8083)
     └── Email Service (8084)
            │
       MongoDB + Kafka
```

Requests flow through Kong. Kong handles auth checking via a custom plugin, then routes to the right service.

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

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
