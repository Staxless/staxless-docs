# Customize Branding

> Make Staxless yours.

---

## The Easy Way

```bash
staxless setup:frontend
```

Walks you through company info, colors, and style presets.

---

## Manual Editing

### Company Info

```typescript
// microservices/frontend/config/brand-config.ts
export const COMPANY_CONFIG = {
  name: 'YourSaaSName',
  tagline: 'Your Tagline Here',
  description: 'What you do',
  website: 'https://yourdomain.com',
  email: 'support@yourdomain.com',
};
```

### Colors

```typescript
export const CUSTOM_STYLE: BrandStyle = {
  colors: {
    primary: '#6366F1',
    secondary: '#8B5CF6',
    background: '#0F172A',
    textPrimary: '#F8FAFC',
  },
};
```

### Social Links

```typescript
export const SOCIAL_CONFIG = {
  twitter: '@YourHandle',
  github: 'https://github.com/you',
  linkedin: 'https://linkedin.com/company/you',
};
```

---

## Style Presets

| Preset | Vibe |
|--------|------|
| `staxless` | Light, lavender purple |
| `linear` | Dark, purple accents |
| `notion` | Light, minimal |
| `vercel` | Dark, black and white |
| `grok` | Light, blue accents |

Apply with `staxless setup:frontend` or copy from `brand-config.ts` templates.

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
