# Brand System

> Use Staxless's centralized branding throughout your app.

---

## The Config Files

Everything lives in `microservices/frontend/config/`:

| File | What it controls |
|------|------------------|
| `brand-config.ts` | Company info, colors, typography |
| `sales-config.ts` | Sales page content, pricing, testimonials |

---

## Using Brand Config

Import and use anywhere in your frontend:

```tsx
import { BRAND_CONFIG } from '@config/brand-config';

export default function Footer() {
  return (
    <footer>
      <p>&copy; {BRAND_CONFIG.company.name}</p>
      <a href={`mailto:${BRAND_CONFIG.company.email}`}>
        {BRAND_CONFIG.company.email}
      </a>
    </footer>
  );
}
```

---

## Available Config

### Company Info

```typescript
BRAND_CONFIG.company.name        // "YourSaaS"
BRAND_CONFIG.company.tagline     // "Your tagline"
BRAND_CONFIG.company.description // "What you do"
BRAND_CONFIG.company.website     // "https://..."
BRAND_CONFIG.company.email       // "support@..."
```

### Colors

```typescript
BRAND_CONFIG.style.colors.primary       // "#6366F1"
BRAND_CONFIG.style.colors.secondary     // "#8B5CF6"
BRAND_CONFIG.style.colors.background    // "#0F172A"
BRAND_CONFIG.style.colors.textPrimary   // "#F8FAFC"
```

### Social Links

```typescript
BRAND_CONFIG.social.twitter   // "@YourHandle"
BRAND_CONFIG.social.github    // "https://..."
BRAND_CONFIG.social.linkedin  // "https://..."
```

---

## Using in Tailwind

The brand colors are exposed as CSS variables. Use them in Tailwind:

```tsx
<button className="bg-primary hover:bg-primary-hover">
  Click me
</button>

<div className="text-text-primary bg-background">
  Content
</div>
```

---

## Dynamic Metadata

Use brand config in page metadata:

```tsx
import { BRAND_CONFIG } from '@config/brand-config';

export const metadata = {
  title: `About - ${BRAND_CONFIG.company.name}`,
  description: BRAND_CONFIG.company.description,
};
```

---

## Sales Page Content

The sales config powers the entire sales page:

```typescript
import { SALES_CONFIG } from '@config/sales-config';

// Hero section
SALES_CONFIG.hero.headline
SALES_CONFIG.hero.subheadline
SALES_CONFIG.hero.cta

// Features
SALES_CONFIG.features.items[]

// Pricing
SALES_CONFIG.purchasePitch.packages[]

// Testimonials
SALES_CONFIG.socialProof.testimonials[]

// FAQ
SALES_CONFIG.faq.items[]
```

---

## Changing Branding

**Quick way:**
```bash
staxless setup:frontend
```

**Manual way:**
Edit the config files directly. Changes are picked up on next build.

---

## Style Presets

The CLI includes presets for common looks:

| Preset | Vibe |
|--------|------|
| `staxless` | Light, lavender purple |
| `linear` | Dark, purple accents |
| `notion` | Light, minimal black |
| `vercel` | Dark, black and white |
| `grok` | Light, blue accents |

Apply with `staxless setup:frontend` and choose a preset.

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
