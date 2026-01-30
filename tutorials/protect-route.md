# Protect a Route

> Require login or subscription for a page.

Making a page require authentication or an active subscription.

---

## Add to Protected Routes

```typescript
// microservices/frontend/src/middleware.ts
const PROTECTED_ROUTES = ['/dashboard', '/checkout', '/your-new-route'];
```

That's it. The middleware handles the rest.

---

## What Happens

| User state | Behavior |
|------------|----------|
| Not logged in | Redirect to `/login` |
| Logged in, no subscription | Redirect to `/checkout` |
| Logged in, active subscription | Access granted |

---

## Need Custom Logic?

The middleware checks subscription status automatically. If you need different behavior, edit the middleware:

```typescript
// frontend/src/middleware.ts
if (isProtected && user.subscription.status !== 'active') {
  // Change this redirect
  return NextResponse.redirect(new URL('/checkout', request.url));
}
```

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
