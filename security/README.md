# Staxless Security

> Enterprise-grade security out of the box.

How Staxless keeps things locked down. Most of this just works automatically.

---

## Architecture Security Benefits

The microservices architecture isn't just for scaling — it's a security feature.

### Defense in Depth

```
Internet
    │
    ▼
┌─────────────────────────────────────┐
│  Cloudflare (DDoS, WAF, TLS)        │
└─────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│  Kong Gateway                        │
│  • Rate limiting                     │
│  • CORS enforcement                  │
│  • Auth validation (RBAC plugin)     │
│  • Request/response transformation   │
└─────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│  Internal Services (not exposed)     │
│  • Auth, User, Stripe, Email         │
│  • Communicate via internal network  │
│  • Each validates its own inputs     │
└─────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│  MongoDB (authenticated, encrypted)  │
└─────────────────────────────────────┘
```

### Blast Radius Containment

If one service is compromised:
- It only has access to its own data
- Other services are unaffected
- The attacker can't pivot through the network
- You can isolate and redeploy just that service

### No Direct Database Access

Frontend never talks to the database. Every request:
1. Goes through Kong (auth check)
2. Gets routed to the right service
3. Service validates and processes
4. Returns only what's needed

---

## What's Handled For You

- **Session management** — Lucia handles secure sessions with HTTP-only cookies
- **CSRF protection** — SameSite cookies, origin checking
- **XSS prevention** — React's built-in escaping, CSP headers
- **SQL injection** — N/A (MongoDB), but Mongoose schemas validate all input
- **Rate limiting** — Kong limits requests per service
- **HTTPS** — Cloudflare tunnel handles TLS termination
- **Secrets** — Docker secrets, never baked into images
- **CORS** — Configured per-origin in Kong

---

## Rate Limiting

Kong applies rate limits per service:

| Service | Per Minute | Per Hour |
|---------|------------|----------|
| Auth | 100 | 1,000 |
| Stripe | 50 | 500 |
| Email | 30 | 300 |
| User | 60 | 600 |

Exceeding limits returns `429 Too Many Requests`.

Configure in `kong-gateway/src/kong.yaml`:

```yaml
plugins:
  - name: rate-limiting
    config:
      minute: 100
      hour: 1000
      policy: local
```

---

## Authentication Flow

1. User submits credentials or OAuth token
2. Auth service validates and creates session
3. Session ID stored in HTTP-only cookie
4. Every subsequent request:
   - Kong extracts session cookie
   - RBAC plugin validates with auth service
   - User info passed to downstream services via header

```yaml
# Kong RBAC plugin
plugins:
  - name: rbac-auth
    config:
      auth_service_url: http://auth-service:8080
      required_roles: [user]
```

Services receive:
```
x-user-info: {"id":"...","email":"...","roles":["user"]}
```

---

## Secrets Management

### Development
- `.env` files (gitignored)
- Template shows required vars without values

### Production
1. `staxless setup:prod` adds secrets to GitHub
2. GitHub Actions injects into Docker Swarm
3. Secrets mounted as files, not env vars
4. Never stored in images

```yaml
# docker-compose.prod.yml
secrets:
  stripe_key:
    external: true
services:
  stripe-service:
    secrets:
      - stripe_key
```

---

## Production Hardening

### Cloudflare Setup

1. **Tunnel** — Services aren't directly exposed. Cloudflare connects to your infrastructure.
2. **WAF** — Blocks common attacks (SQLi, XSS, etc.)
3. **DDoS** — Absorbs volumetric attacks
4. **Bot Management** — Filters malicious bots

### Database Security

1. MongoDB Atlas with authentication required
2. IP allowlist (or VPC peering)
3. Encryption at rest and in transit
4. Regular automated backups

### Container Security

- Non-root users in containers
- Read-only file systems where possible
- Resource limits prevent runaway processes
- No SSH access to production containers

---

## Pre-Production Checklist

**Infrastructure:**
- [ ] Cloudflare tunnel configured
- [ ] MongoDB Atlas with strong credentials
- [ ] IP restrictions on database
- [ ] GitHub Secrets populated

**Application:**
- [ ] CORS origins updated to production domain
- [ ] OAuth redirect URIs point to production
- [ ] Stripe webhooks configured for production
- [ ] Mailgun domain verified (not sandbox)
- [ ] Strong `PAYLOAD_SECRET` set

**Monitoring:**
- [ ] Error tracking (Sentry) configured
- [ ] Uptime monitoring active
- [ ] Log aggregation set up

---

## Security Headers

Kong adds security headers automatically:

```
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Referrer-Policy: strict-origin-when-cross-origin
```

Add CSP in your Next.js config for additional protection.

---

## Reporting Vulnerabilities

Found something? Email security@staxless.dev. We take this seriously.

---

<p align="center">
  <a href="../README.md">← Staxless Docs</a>
</p>
