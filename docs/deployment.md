# Deployment Guide

How to deploy Pumpy World to production.

## Vercel (recommended)

Pumpy World uses Vercel with the included `vercel.json` config.

### First deploy

1. Push the game repo to GitHub
2. Import the project in [Vercel](https://vercel.com)
3. Framework: **Vite** (auto-detected)
4. Build: `npm run build`
5. Output: `dist`
6. Deploy

### Custom domain

Add these domains in Vercel project settings:

- `pumpyworld.space` (apex)
- `www.pumpyworld.space` (www)

#### DNS records

| Type | Name | Value |
|------|------|-------|
| A | `@` | Vercel apex IP (shown in Vercel dashboard) |
| CNAME | `www` | `cname.vercel-dns.com` |

### CLI deploy

```bash
npm install -g vercel
vercel --prod
```

## Build output

`npm run build` produces a static site in `dist/`:

- Bundled JS with tree-shaking
- Inlined / hashed assets
- GLSL shaders compiled by vite-plugin-glsl

## Cache headers

`vercel.json` sets long cache for `/assets/*` and no-cache for `/.well-known/*`.

## Multiplayer relay

The game client is static; the WebSocket relay (`server/`) must be hosted separately.

### Deploy relay to Fly.io (example)

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY server/package*.json ./
RUN npm ci --omit=dev
COPY server/server.js .
ENV PORT=8080
EXPOSE 8080
CMD ["node", "server.js"]
```

Then point players to:

```
https://pumpyworld.space/?server=wss://your-relay.fly.dev
```

### Environment variables

| Var | Default | Description |
|-----|---------|-------------|
| `PORT` | `8080` | WebSocket server port |

## Social / SEO

Meta tags in `index.html` configure Open Graph and Twitter cards:

- Title: Pumpy World
- Image: `https://pumpyworld.space/social/share-1200x630.png`
- Site: @pumpyworld

## Checklist

- [ ] `npm run build` succeeds locally
- [ ] Custom domains configured in Vercel
- [ ] DNS propagated (check with `dig pumpyworld.space`)
- [ ] HTTPS works on apex and www
- [ ] Social share image loads
- [ ] (Optional) Multiplayer relay deployed with WSS
