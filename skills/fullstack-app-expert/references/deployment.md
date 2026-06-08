# Deployment & Infrastructure (2025–2026)

## Platform Decision Framework

| Platform | Best For | Cold Start | Pricing Model | Scale |
|---|---|---|---|---|
| **Vercel** | Next.js apps, DX-first teams | <100ms (Fluid Compute) | Usage-based, pricey at scale | 100 edge regions |
| **Cloudflare Workers** | Edge APIs, global perf, cost at scale | <5ms (V8 isolates) | $5/mo + $0.30/M requests | 300+ PoPs |
| **Fly.io** | Persistent servers, global, full control | 2–5s (container) | Per-minute compute | Global |
| **Railway** | MVPs, indie hackers, simplicity | 2–5s (container) | Per-minute compute | Single region |
| **AWS Lambda** | AWS ecosystem, event-driven | 100ms–1s (warm), 1–3s (cold) | Per-invocation | Global |

---

## Vercel

Vercel's **Fluid Compute** (2025) changed the cold-start calculus — bytecode caching + predictive instance warming brings cold starts to near-zero for most Next.js routes.

**When to use Vercel:**
- Next.js apps (first-party integration, PPR support, preview deployments)
- Teams that prioritize DX over cost optimization
- Apps with moderate traffic (under 50M requests/month cost-effectively)

**When Vercel gets expensive:**
- High request volume (bandwidth charges, function invocations)
- Long-running computations (use Fly.io for those instead)
- Background jobs (use Railway or Fly.io)

```typescript
// next.config.js — Vercel-specific optimizations
const nextConfig = {
  experimental: { ppr: 'incremental' }, // PPR on Vercel is most stable
  images: { formats: ['image/avif', 'image/webp'] },
};
```

**Vercel Edge Middleware** — run code before every request, at the edge:
```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // Geolocation, A/B testing, auth redirects, rewriting — all at edge
  const country = request.geo?.country;
  if (country === 'DE') {
    return NextResponse.rewrite(new URL('/de' + request.nextUrl.pathname, request.url));
  }
}
export const config = { matcher: '/((?!_next/static|favicon).*)' };
```

---

## Cloudflare Workers

**The cost case:** At 50M+ requests/month, Cloudflare's $0.30/M flat pricing (no bandwidth overages) creates a large gap over Vercel. Workers start in under 5ms (V8 isolates, no containers).

**Hono + Cloudflare Workers — the edge API combo:**
```typescript
// worker.ts
import { Hono } from 'hono';
import { D1Database } from '@cloudflare/workers-types';

type Bindings = {
  DB: D1Database;      // Cloudflare D1 (SQLite at edge)
  KV: KVNamespace;     // Cloudflare KV
  R2: R2Bucket;        // Object storage
};

const app = new Hono<{ Bindings: Bindings }>();

app.get('/products', async (c) => {
  const products = await c.env.DB.prepare('SELECT * FROM products').all();
  return c.json(products.results);
});

export default app;
```

**Cloudflare's full-stack offering:**
- **D1** — SQLite-based edge database (good for read-heavy, small datasets)
- **KV** — Key-value store, globally replicated (eventually consistent)
- **R2** — S3-compatible object storage (zero egress fees)
- **Durable Objects** — Stateful serverless (WebSockets, coordination)
- **Queues** — Message queuing

**Limitation:** No Node.js APIs (use Workers-compatible libraries). Long-running tasks limited to 30s CPU time.

---

## Fly.io

Fly.io runs containerized apps in full VMs across 30+ global regions. Best for:
- Persistent servers (WebSockets, background jobs, stateful services)
- When you need a real Linux environment
- Cost-efficient at sustained medium traffic
- Databases (deploy PostgreSQL, Redis on Fly)

```dockerfile
# Dockerfile for a Hono/Node.js app on Fly.io
FROM node:22-alpine AS builder
WORKDIR /app
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build

FROM node:22-alpine AS runner
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

```toml
# fly.toml
app = "my-api"
primary_region = "iad"

[[services]]
  internal_port = 3000
  protocol = "tcp"

  [[services.ports]]
    port = 443
    handlers = ["tls", "http"]

# Auto-scale based on concurrent connections
[services.concurrency]
  type = "connections"
  hard_limit = 25
  soft_limit = 20
```

---

## Railway

Railway is the simplest persistent server deployment:
- Beautiful dashboard, git-push deploys
- Built-in databases (PostgreSQL, Redis, MySQL)
- Single region (limitation vs Fly.io)
- Best for MVPs, side projects, internal tools

```
# Deploy in 30 seconds
railway up
railway add postgresql
```

---

## Docker Best Practices (Multi-Stage Builds)

Always use multi-stage builds for production Node.js apps:

```dockerfile
# Multi-stage build — production image is minimal
FROM node:22-alpine AS deps
WORKDIR /app
COPY package*.json .
RUN npm ci --only=production

FROM node:22-alpine AS builder
WORKDIR /app
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build

FROM node:22-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
# Non-root user for security
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
COPY --from=deps /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
USER nextjs
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

**Key practices:**
- Pin exact base image version in production
- Use non-root user
- Copy only necessary files (use `.dockerignore`)
- `npm ci` not `npm install` (reproducible installs)

---

## Edge Computing Patterns

**What runs at the edge (per-request, <5ms):**
- Auth token verification + redirects
- Geolocation-based routing
- A/B testing header injection
- Rate limiting (with Upstash Redis)
- Simple data transformations

**What does NOT belong at the edge:**
- Heavy database queries (latency to main DB region negates edge benefit)
- File processing, image manipulation
- Complex business logic

**The CDN Strategy:**
- Static assets → CDN always (Next.js does this automatically)
- API responses → Cache at CDN for public data with appropriate TTLs
- Personalized content → Never cache at CDN (use streaming + RSC)
- Images → Next.js `<Image>` + Cloudflare Images or Cloudinary for transforms
