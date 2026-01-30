# Authentication

> Lucia-powered auth with OAuth and magic links.

How login works under the hood. You probably only need this if you're customizing the auth flow or debugging.

---

## The Flow

```
User clicks login
     ↓
Frontend → Kong Gateway
     ↓
/oauth/{provider}
     ↓
Auth Service (Lucia)
     ↓
OAuth Provider (Google/GitHub)
     ↓
Callback → Session created
     ↓
Cookie set (auth_session)
     ↓
Redirect to dashboard
```

---

## Key Files

| File | What it does |
|------|--------------|
| `auth-service/src/routes/oauth-router.ts` | OAuth flow |
| `auth-service/src/routes/magic-link-router.ts` | Email login |
| `auth-service/src/config/lucia-config.ts` | Session stuff |
| `frontend/src/lib/validate-user.tsx` | User validation |
| `frontend/src/middleware.ts` | Route protection |

---

## Methods

**OAuth (Google, GitHub)** — Pre-configured. Just add your credentials to the env file.

**Magic Links** — Passwordless email login via Mailgun. Needs `MAILGUN_API_KEY` and `MAILGUN_DOMAIN`.

---

## Session Validation

Every protected request validates the session:

```typescript
// frontend/src/lib/validate-user.tsx
export async function getUser(): Promise<ActionResult<User>> {
  const cookieStore = await cookies();
  const sessionCookie = cookieStore.get('auth_session');

  const data = await safeFetch(`${getApiGatewayUrl()}/auth/validate-request`, {
    method: 'POST',
    headers: { Cookie: `auth_session=${sessionCookie.value}` },
  });

  return success(data.user);
}
```

---

## User Object

```typescript
interface User {
  id: string;
  email: string;
  roles: string[];
  subscription: {
    type: string;
    startDate: Date;
    status: 'active' | 'inactive' | 'pending';
  };
}
```

---

## API Endpoints

| Endpoint | Method | What it does |
|----------|--------|--------------|
| `/oauth/providers` | GET | List available providers |
| `/oauth/{provider}` | GET | Start OAuth flow |
| `/oauth/{provider}/callback` | GET | Handle callback |
| `/auth/magic-link/register` | POST | Send registration email |
| `/auth/magic-link/login` | POST | Send login email |
| `/auth/magic-link/callback` | GET | Validate magic link |
| `/auth/logout` | POST | End session |
| `/auth/validate-request` | POST | Check session |

---

<p align="center">
  <a href="./README.md">← Staxless Features</a>
</p>
