# staxless generate

> Scaffold new resources from templates.

Aliases: `staxless g`

---

## Commands

| Command | Description |
|---------|-------------|
| `staxless generate service <name>` | Create a new microservice |

---

## staxless generate service

Create a new microservice with all the boilerplate.

```bash
staxless generate service <name>
```

### Options

| Option | Description |
|--------|-------------|
| `--no-kafka` | Skip Kafka configuration |
| `--dry-run` | Preview without creating files |

---

## Examples

### Create a notification service

```bash
staxless generate service notification-service
```

### Create without Kafka

```bash
staxless generate service simple-api --no-kafka
```

### Preview what would be created

```bash
staxless generate service my-service --dry-run
```

### Using the shorthand

```bash
staxless g service payment-service
```

---

## What you get

```
my-service/
├── src/
│   ├── config/
│   │   ├── db-config.ts        # MongoDB setup
│   │   ├── kafka-client.ts     # Kafka producer/consumer
│   │   └── mongo-db-client.ts  # Database implementation
│   ├── consumers/
│   │   ├── handlers/
│   │   │   └── index.ts        # Event handler registry
│   │   └── index.ts            # Consumer startup
│   ├── lib/
│   │   ├── database.ts         # Database abstraction
│   │   └── secrets-manager.ts  # Docker secrets
│   ├── middleware/
│   │   ├── error-handler.ts    # Error handling
│   │   └── request-logger.ts   # Request logging
│   ├── models/
│   │   └── example-model.ts    # Example CRUD model
│   ├── routes/
│   │   ├── api-router.ts       # Your API routes
│   │   ├── health-router.ts    # Health check
│   │   └── index.ts
│   ├── services/
│   │   └── example-service.ts  # Business logic
│   └── server.ts               # Entry point
├── secrets/
│   └── .env.example
├── package.json
├── tsconfig.json
└── README.md
```

---

## After generating

```bash
# 1. Go to the service
cd microservices/<service-name>

# 2. Set up environment
cp secrets/.env.example secrets/.env

# 3. Install dependencies
npm install

# 4. Start development
npm run dev

# 5. Test health endpoint
curl http://localhost:8080/health
```

---

## Naming rules

Service names must be:
- Lowercase
- Kebab-case (words separated by hyphens)
- Start with a letter
- 2-50 characters
- Letters, numbers, and hyphens only

**Good:** `payment-service`, `user-api`, `notifications`

**Bad:** `PaymentService`, `user_api`, `123service`

---

## Customization

After generating, modify these files:

| File | Purpose |
|------|---------|
| `src/routes/api-router.ts` | Add your endpoints |
| `src/models/` | Add your data models |
| `src/services/` | Add business logic |
| `src/consumers/handlers/index.ts` | Add Kafka handlers |
| `secrets/.env.example` | Add env vars |

---

## Add to Docker Compose

After creating, add to `docker/compose/docker-compose.dev.yml`:

```yaml
my-service:
  image: app_my_service
  build:
    context: ../../microservices/my-service/
    dockerfile: ../../docker/dockerfiles/node-service.Dockerfile
    target: dev
  env_file:
    - ../../environments/dev/staxless.dev.env
  environment:
    DATABASE_URL: mongodb://mongodb-service:27017
    NODE_ENV: development
    KAFKA_ENABLED: "true"
  ports:
    - "8085:8080"
  volumes:
    - ../../microservices/my-service/src:/home/node/src
  networks:
    backend:
```

---

<p align="center">
  <a href="./README.md">← CLI Reference</a>
</p>
