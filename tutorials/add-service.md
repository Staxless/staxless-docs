# Add a New Service

> Create microservices instantly with the CLI, or build from scratch.

Two options: use the generator (recommended) or build manually.

---

## Option 1: Use the Service Generator (Recommended)

The fastest way to create a production-ready microservice.

```bash
staxless generate service <service-name>

# Or use the shorthand:
staxless g service <service-name>

# Examples:
staxless generate service notification-service
staxless generate service analytics-service
staxless generate service billing-service
```

### Options

| Flag | Description |
|------|-------------|
| `--no-kafka` | Create service without Kafka configuration |
| `--dry-run` | Preview what would be created |

### What You Get

The generator creates a complete service with:

| Component | Description |
|-----------|-------------|
| Express server | CORS, JSON parsing, error handling pre-configured |
| MongoDB integration | Database abstraction with connection pooling |
| Kafka support | Producer/consumer setup with event registry |
| Health check | `/health` endpoint for monitoring |
| TypeScript | Strict mode with ES modules |
| Docker ready | Works with existing multi-stage Dockerfile |

### Generated Structure

```
my-service/
├── src/
│   ├── config/           # DB, Kafka configuration
│   │   ├── db-config.ts
│   │   ├── kafka-client.ts
│   │   └── mongo-db-client.ts
│   ├── consumers/        # Kafka event handlers
│   │   ├── handlers/
│   │   │   └── index.ts  # Event registry
│   │   └── index.ts      # Consumer startup
│   ├── lib/              # Shared utilities
│   │   ├── database.ts   # BaseDatabase abstraction
│   │   └── secrets-manager.ts
│   ├── middleware/       # Express middleware
│   │   ├── error-handler.ts
│   │   └── request-logger.ts
│   ├── models/           # Data models
│   │   └── example-model.ts
│   ├── routes/           # API endpoints
│   │   ├── api-router.ts
│   │   ├── health-router.ts
│   │   └── index.ts
│   ├── services/         # Business logic
│   │   └── example-service.ts
│   └── server.ts         # Entry point
├── secrets/
│   └── .env.example
├── package.json
├── tsconfig.json
└── README.md
```

### After Generation

```bash
cd microservices/<service-name>
cp secrets/.env.example secrets/.env
npm install
npm run dev
```

Test it:

```bash
curl http://localhost:8080/health
```

---

## Option 2: Build from Scratch

For when you need full control or a minimal service.

### 1. Create the Service Directory

```bash
mkdir -p microservices/notification-service/src/{routes,models,consumers,config}
```

---

### 2. Set Up package.json

```json
// microservices/notification-service/package.json
{
  "name": "notification-service",
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js"
  },
  "dependencies": {
    "express": "^4.21.1",
    "mongodb": "^6.9.0",
    "kafkajs": "^2.2.4",
    "cors": "^2.8.5",
    "dotenv": "^16.4.5"
  },
  "devDependencies": {
    "typescript": "^5.9.3",
    "tsx": "^4.20.6",
    "@types/express": "^5.0.2",
    "@types/node": "^22.7.4",
    "@types/cors": "^2.8.18"
  }
}
```

---

### 3. Create the Server

```typescript
// microservices/notification-service/src/server.ts
import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';
import notificationRoutes from './routes/notification-routes.js';
import { startKafkaConsumer } from './consumers/notification-consumer.js';
import { initializeDatabase } from './config/db-config.js';

dotenv.config({ path: './secrets/.env' });

const app = express();
app.use(express.json());
app.use(cors({
  origin: process.env.FRONTEND_URL ?? 'http://localhost:3000',
  credentials: true,
}));

// Health check
app.get('/health', (req, res) => {
  res.json({
    uptime: process.uptime(),
    message: 'OK',
    timestamp: new Date().toISOString(),
  });
});

// Routes
app.use('/', notificationRoutes);

const PORT = process.env.PORT || 8085;

async function startServer() {
  try {
    // 1. Database first (critical)
    await initializeDatabase();
    console.log('Database connected');

    // 2. Start server immediately
    app.listen(PORT, () => {
      console.log(`Service running on port ${PORT}`);
    });

    // 3. Kafka in background (non-blocking)
    startKafkaConsumer().catch(console.error);
  } catch (error) {
    console.error('Failed to start:', error);
    process.exit(1);
  }
}

startServer();
```

---

### 4. Add a Model

```typescript
// microservices/notification-service/src/models/notification-model.ts
import type { Collection, Db } from 'mongodb';
import { database } from '../config/db-config.js';

export interface NotificationType {
  _id: string;
  userId: string;
  type: string;
  title: string;
  message: string;
  read: boolean;
  createdAt: Date;
}

const getCollection = async (): Promise<Collection<NotificationType>> => {
  const db: Db = await database.getClient();
  return db.collection<NotificationType>('notifications');
};

export const Notification = {
  async create(data: Omit<NotificationType, '_id' | 'createdAt' | 'read'>) {
    const collection = await getCollection();
    const doc: NotificationType = {
      _id: crypto.randomUUID(),
      ...data,
      read: false,
      createdAt: new Date(),
    };
    await collection.insertOne(doc);
    return doc;
  },

  async findByUser(userId: string, limit = 50) {
    const collection = await getCollection();
    return collection.find({ userId })
      .sort({ createdAt: -1 })
      .limit(limit)
      .toArray();
  },

  async markRead(id: string) {
    const collection = await getCollection();
    return collection.updateOne({ _id: id }, { $set: { read: true } });
  },
};
```

---

### 5. Add Routes

```typescript
// microservices/notification-service/src/routes/notification-routes.ts
import { Router } from 'express';
import { Notification } from '../models/notification-model.js';

const router = Router();

// Get user's notifications
router.get('/notifications', async (req, res) => {
  const userInfo = req.headers['x-user-info'];
  if (!userInfo) {
    return res.status(401).json({ error: 'Unauthorized' });
  }

  const user = JSON.parse(userInfo as string);
  const notifications = await Notification.findByUser(user.id);
  res.json({ notifications });
});

// Mark as read
router.post('/notifications/:id/read', async (req, res) => {
  await Notification.markRead(req.params.id);
  res.json({ success: true });
});

export default router;
```

---

### 6. Add Kafka Consumer

```typescript
// microservices/notification-service/src/consumers/notification-consumer.ts
import { Kafka } from 'kafkajs';
import { Notification } from '../models/notification-model.js';

const kafka = new Kafka({
  clientId: 'notification-service',
  brokers: [process.env.KAFKA_BROKER || 'kafka-service:9092'],
});

export async function startKafkaConsumer() {
  const consumer = kafka.consumer({ groupId: 'notification-group' });

  await consumer.connect();
  await consumer.subscribe({ topic: 'notification-events', fromBeginning: false });

  await consumer.run({
    eachMessage: async ({ message }) => {
      const event = JSON.parse(message.value!.toString());

      if (event.type === 'CREATE_NOTIFICATION') {
        await Notification.create(event.payload);
        console.log('Notification created:', event.payload.title);
      }
    },
  });

  console.log('Kafka consumer started');
}
```

---

### 7. Add to Docker Compose

```yaml
# docker/compose/docker-compose.dev.yml
notification-service:
  image: app_notification_service
  build:
    context: ../../microservices/notification-service/
    dockerfile: ../../docker/dockerfiles/node-service.Dockerfile
    target: dev
  env_file:
    - ../../environments/dev/staxless.dev.env
  environment:
    DATABASE_URL: mongodb://mongodb-service:27017
    NODE_ENV: development
    KAFKA_ENABLED: "true"
  ports:
    - "8085:8085"
  volumes:
    - ../../microservices/notification-service/src:/home/node/src
  networks:
    backend:
      aliases:
        - "notification"
```

---

### 8. Register in Kong

```yaml
# microservices/kong-gateway/src/kong.yaml
- name: notification-service
  url: http://notification-service:8085
  routes:
    - name: notifications
      methods: [GET, POST]
      paths: [/notifications]
      plugins:
        - name: rbac-auth
          config:
            auth_service_url: http://auth-service:8080
            required_roles: [user]
```

---

### 9. Restart and Test

```bash
staxless dev down && staxless dev start
```

Test the endpoint:

```bash
curl http://localhost:8000/notifications \
  -H "Cookie: auth_session=xxx"
```

---

## Publishing Events to Your Service

Other services can trigger actions via Kafka:

```typescript
// From any other service
import { createProducer } from './config/kafka-client.js';

const producer = createProducer();
await producer.connect();

await producer.send({
  topic: 'notification-events',
  messages: [{
    key: 'notification.create',
    value: JSON.stringify({
      type: 'CREATE_NOTIFICATION',
      payload: {
        userId: 'user_123',
        type: 'payment',
        title: 'Payment Received',
        message: 'Your payment of $99 was successful.',
      }
    })
  }]
});
```

---

## Customizing Generated Services

After running the generator, customize these files:

| File | What to Change |
|------|----------------|
| `src/routes/api-router.ts` | Add your API endpoints |
| `src/models/example-model.ts` | Replace with your data model |
| `src/services/example-service.ts` | Add business logic |
| `src/consumers/handlers/index.ts` | Register Kafka event handlers |
| `secrets/.env.example` | Add service-specific env vars |

---

## Architecture Notes

### Startup Sequence

Services follow a non-blocking startup:

1. **Database** — Connects first (critical)
2. **Express** — Starts immediately after DB
3. **Kafka** — Connects in background with retry

This ensures the service responds to health checks even if Kafka is temporarily down.

### Error Handling

Use the HttpError class for custom status codes:

```typescript
import { HttpError } from './middleware/error-handler.js';

throw new HttpError('Not found', 404);
throw new HttpError('Forbidden', 403);
```

### Secrets in Production

In production, secrets load from Docker secrets:

```typescript
import { loadSecret } from './lib/secrets-manager.js';

const apiKey = loadSecret('API_KEY') ?? process.env.API_KEY;
```

---

Your service now:
- Has its own API routes
- Stores data in MongoDB
- Listens to Kafka events
- Is protected by Kong's auth
- Scales independently

---

<p align="center">
  <a href="./README.md">← Tutorials</a>
</p>
