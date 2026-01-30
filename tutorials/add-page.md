# Add a Page

> Create a new route in your Staxless app.

Adding a new route like `/about` or `/pricing`.

---

## Create the Page

```bash
mkdir -p microservices/frontend/src/app/about
```

```tsx
// microservices/frontend/src/app/about/page.tsx
import { BRAND_CONFIG } from '@config/brand-config';

export const metadata = {
  title: `About - ${BRAND_CONFIG.company.name}`,
  description: BRAND_CONFIG.company.description,
};

export default function AboutPage() {
  return (
    <main className="container mx-auto px-4 py-16">
      <h1 className="text-4xl font-bold mb-6">About Us</h1>
      <p className="text-text-secondary">
        {BRAND_CONFIG.company.description}
      </p>
    </main>
  );
}
```

Visit http://localhost:3000/about. Done.

---

## Using Brand Colors

The brand system exposes colors as Tailwind classes:

```tsx
<div className="bg-background text-text-primary">
  <h1 className="text-primary">Branded Heading</h1>
  <p className="text-text-secondary">Secondary text</p>
  <button className="bg-primary hover:bg-primary-hover">
    Action
  </button>
</div>
```

See [Brand System](./brand-system.md) for the full config.

---

## Need Layout?

Wrap with shared components:

```tsx
import { Header } from '@/components/shared/header';
import { Footer } from '@/components/shared/footer';

export default function AboutPage() {
  return (
    <>
      <Header />
      <main className="container mx-auto px-4 py-16">
        {/* content */}
      </main>
      <Footer />
    </>
  );
}
```

---

## Protected Page?

See [Protect a Route](./protect-route.md).

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
