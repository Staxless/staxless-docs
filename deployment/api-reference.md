# API Reference

> Complete Staxless API documentation.

All requests go through Kong at `http://localhost:8000` (dev) or `https://api.yourdomain.com` (prod).

---

## Authentication

### OAuth

| Endpoint | Method | What it does |
|----------|--------|--------------|
| `/oauth/providers` | GET | List available providers |
| `/oauth/register/{provider}` | GET | Start OAuth registration |
| `/oauth/login/{provider}` | GET | Start OAuth login |
| `/oauth/{provider}/callback` | GET | Handle callback |

```bash
curl http://localhost:8000/oauth/providers
# {"providers": ["google", "github"]}
```

### Magic Links

| Endpoint | Method | What it does |
|----------|--------|--------------|
| `/auth/magic-link/register` | POST | Send registration email |
| `/auth/magic-link/login` | POST | Send login email |
| `/auth/magic-link/callback` | GET | Validate magic link |

```bash
curl -X POST http://localhost:8000/auth/magic-link/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com"}'
```

### Session

| Endpoint | Method | What it does |
|----------|--------|--------------|
| `/auth/validate-request` | POST | Validate session |
| `/auth/logout` | POST | End session |

```bash
curl -X POST http://localhost:8000/auth/validate-request \
  -H "Cookie: auth_session=xxx"
```

---

## Payments

| Endpoint | Method | Auth | What it does |
|----------|--------|------|--------------|
| `/products` | GET | No | List Stripe products |
| `/checkout` | POST | Yes | Create checkout session |
| `/checkout/session/:id` | GET | Yes | Get session details |
| `/webhook` | POST | No | Stripe webhook receiver |

```bash
curl -X POST http://localhost:8000/checkout \
  -H "Content-Type: application/json" \
  -H "Cookie: auth_session=xxx" \
  -d '{"priceId": "price_xxx", "successUrl": "...", "cancelUrl": "..."}'
```

---

## User

| Endpoint | Method | Auth | What it does |
|----------|--------|------|--------------|
| `/user/settings` | GET | Yes | Get user profile |

```bash
curl http://localhost:8000/user/settings \
  -H "Cookie: auth_session=xxx"
```

---

## Health

Every service exposes `/health`:

```bash
curl http://localhost:8080/health  # Auth
curl http://localhost:8082/health  # User
curl http://localhost:8083/health  # Stripe
curl http://localhost:8084/health  # Email
```

---

## Rate Limits

| Service | Per Minute | Per Hour |
|---------|------------|----------|
| Auth | 100 | 1,000 |
| Stripe | 50 | 500 |
| Email | 30 | 300 |
| User | 60 | 600 |

Headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 1640000000
```

---

## Auth Header

Kong validates sessions and passes user info to services:

```
x-user-info: {"id":"...","email":"...","roles":["user"]}
```

Backend services read this:

```typescript
const user = JSON.parse(req.headers['x-user-info'] as string);
```

---

## Errors

```json
{
  "error": "Error message",
  "code": "ERROR_CODE"
}
```

| Code | Meaning |
|------|---------|
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 429 | Rate Limited |
| 500 | Server Error |

---

<p align="center">
  <a href="./README.md">← Staxless Deployment</a>
</p>
