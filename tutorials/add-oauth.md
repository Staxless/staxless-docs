# Add OAuth Provider

> Social login with 50+ providers via Arctic.

Staxless uses [Arctic](https://arcticjs.dev/) for OAuth, which supports 50+ providers out of the box.

---

## Pre-Configured Providers

Google and GitHub are ready to go. Just add credentials:

```bash
# environments/dev/staxless.dev.env

GOOGLE_CLIENT_ID=xxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxx
GOOGLE_REDIRECT_URI=http://localhost:8000/oauth/google/callback

GITHUB_CLIENT_ID=xxx
GITHUB_CLIENT_SECRET=xxx
```

Restart and the login buttons work.

---

## Get Credentials

**Google:**
1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. APIs & Services → Credentials → Create OAuth Client ID
3. Set redirect URI: `http://localhost:8000/oauth/google/callback`

**GitHub:**
1. Go to [GitHub Developer Settings](https://github.com/settings/developers)
2. OAuth Apps → New OAuth App
3. Set callback URL: `http://localhost:8000/oauth/github/callback`

---

## Supported Providers

Arctic supports all of these. Adding a new one takes about 10 minutes:

**Popular:**
- Google, GitHub, Microsoft, Apple, Facebook, Twitter/X
- Discord, Slack, Twitch, Spotify, LinkedIn

**Developer Tools:**
- GitLab, Bitbucket, Gitea, Figma, Notion, Linear

**Gaming:**
- Steam, Epic Games, Battle.net, Roblox, osu!

**Enterprise:**
- Okta, Auth0, KeyCloak, Microsoft Entra ID, Salesforce

**And more:**
Amazon Cognito, AniList, Atlassian, Coinbase, Dropbox, Patreon, Reddit, TikTok, Yahoo, Zoom... [full list](https://arcticjs.dev/)

---

## Adding a New Provider

Let's add Discord as an example.

### 1. Get Credentials

Go to [Discord Developer Portal](https://discord.com/developers/applications), create an app, and get your Client ID and Secret.

### 2. Add Environment Variables

```bash
# environments/dev/staxless.dev.env
DISCORD_CLIENT_ID=xxx
DISCORD_CLIENT_SECRET=xxx
DISCORD_REDIRECT_URI=http://localhost:8000/oauth/discord/callback
```

### 3. Create Provider Config

```typescript
// microservices/auth-service/src/providers/discord.ts
import { Discord } from 'arctic';

export const discord = new Discord(
  process.env.DISCORD_CLIENT_ID!,
  process.env.DISCORD_CLIENT_SECRET!,
  process.env.DISCORD_REDIRECT_URI!
);

export const discordScopes = ['identify', 'email'];
```

### 4. Add Routes

```typescript
// microservices/auth-service/src/routes/oauth-router.ts

// Add to the providers list
import { discord, discordScopes } from '../providers/discord.js';

// Login route
router.get('/oauth/discord', async (req, res) => {
  const state = generateState();
  const url = await discord.createAuthorizationURL(state, discordScopes);
  // ... set cookies, redirect
});

// Callback route
router.get('/oauth/discord/callback', async (req, res) => {
  // ... validate, get tokens, create session
});
```

### 5. Add Login Button

```tsx
// frontend/src/components/auth/oauth-button.tsx
<OAuthButton provider="discord" icon={DiscordIcon}>
  Continue with Discord
</OAuthButton>
```

### 6. Restart

```bash
staxless dev down && staxless dev start
```

---

## Provider Template

Check `microservices/auth-service/src/providers/template/` for a starter template you can copy for any new provider.

---

## Production Redirect URIs

Remember to update redirect URIs for production:

```bash
GOOGLE_REDIRECT_URI=https://api.yourdomain.com/oauth/google/callback
DISCORD_REDIRECT_URI=https://api.yourdomain.com/oauth/discord/callback
```

---

<p align="center">
  <a href="./README.md">← Staxless Tutorials</a>
</p>
