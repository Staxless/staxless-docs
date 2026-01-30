# Add a Database Model

> Store new data types in Staxless.

We'll create a Post model as an example.

---

## Create the Model

```typescript
// microservices/user-service/src/models/post-model.ts
import mongoose from 'mongoose';

const postSchema = new mongoose.Schema({
  title: { type: String, required: true },
  content: { type: String, required: true },
  slug: { type: String, required: true, unique: true },
  authorId: { type: String, required: true },
  published: { type: Boolean, default: false },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

export const Post = mongoose.model('Post', postSchema);
```

---

## Use It

```typescript
import { Post } from '../models/post-model.js';

// Create
await Post.create({ title, content, slug, authorId });

// Read
await Post.find({ published: true }).sort({ createdAt: -1 });

// Update
await Post.findByIdAndUpdate(id, { title, content });

// Delete
await Post.findByIdAndDelete(id);
```

---

## Where to Put It

Models go in the service that owns the data. User-related stuff in `user-service`, payment stuff in `stripe-service`, etc.

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
