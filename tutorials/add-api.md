# Add an API Endpoint

> Create backend functionality in Staxless.

We'll add a `/user/profile` endpoint as an example.

---

## 1. Create the Route

```typescript
// microservices/user-service/src/routes/profile-routes.ts
import { Router } from 'express';

const router = Router();

router.get('/user/profile', async (req, res) => {
  const userInfo = req.headers['x-user-info'];
  if (!userInfo) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  const user = JSON.parse(userInfo as string);
  res.json({ user });
});

export default router;
```

---

## 2. Register It

```typescript
// microservices/user-service/src/server.ts
import profileRoutes from './routes/profile-routes.js';
app.use('/', profileRoutes);
```

---

## 3. Add Kong Route (if public-facing)

```yaml
# microservices/kong-gateway/src/kong.yaml
- name: user-profile
  methods: [GET]
  paths: [/user/profile]
  plugins:
    - name: rbac-auth
      config:
        auth_service_url: http://auth-service:8080
        required_roles: [user]
```

---

## 4. Restart

```bash
staxless dev down && staxless dev start
```

Test it:

```bash
curl http://localhost:8000/user/profile
```

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
