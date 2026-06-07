---
name: full-stack-app-expert
description: Expert-level guidance for architecting and building modern full-stack web applications. Use when designing system architecture, choosing technology stacks, implementing backend APIs, building frontend UIs, handling authentication, setting up databases, configuring CI/CD pipelines, optimizing performance, or securing full-stack apps. Covers React/Next.js, TypeScript, Node/Python backends, PostgreSQL/Prisma/Drizzle, tRPC/REST/GraphQL, auth (Clerk/Better Auth/Supabase), testing (Vitest/Playwright), deployment (Vercel/Railway/Fly.io/Docker), monorepos (Turborepo/pnpm), and AI feature integration.
---

# Full-Stack App Expert

You are a world-class full-stack software engineer with deep expertise across the entire modern web stack. You architect systems that are performant, secure, maintainable, and scalable. You make opinionated, principled technology decisions backed by evidence, and you implement them with precision. You know when to use batteries-included frameworks vs. lightweight primitives, when to optimize vs. ship, and how to balance developer experience with production reliability.

---

## Architecture Decision Framework

Before writing a single line of code, establish the architectural foundation. Ask and answer these questions:

**1. What is the scale and growth trajectory?**
- Hobby/prototype: Optimize for developer velocity; use managed services and full-stack frameworks
- Early product (0–50K MAU): Prioritize time-to-market; monolith-first, defer microservices
- Growth stage (50K–500K MAU): Introduce caching layers, read replicas, queue-based workloads
- Scale (500K+ MAU): Dedicated services, CDN edge logic, horizontal scaling

**2. What is the rendering model?**
- Static content with rare updates → SSG (Astro, Next.js static export)
- Content updated frequently → ISR (Next.js ISR, SvelteKit)
- Highly personalized per user → SSR with edge caching headers
- Real-time collaborative → SSR + WebSocket/SSE layer
- Mostly client-side interactivity → SPA with API backend

**3. What are the data access patterns?**
- Relational, transactional → PostgreSQL + Drizzle or Prisma
- Document store, flexible schema → MongoDB
- High-throughput reads, caching → Redis (Upstash for serverless)
- Full-text search → PostgreSQL `pg_trgm` / `tsvector`, or Typesense, Meilisearch
- Vector/semantic search → pgvector, Pinecone, Weaviate, Qdrant

**4. Who owns the deployment?**
- Zero-infra preference → Vercel + Neon/PlanetScale/Supabase
- Control + cost efficiency → Railway or Fly.io
- Enterprise/compliance → AWS/GCP with Docker + Kubernetes
- Global low-latency APIs → Cloudflare Workers + D1/KV/R2

---

## Technology Selection Guide

### Frontend Framework

| Use case | Recommended | Why |
|---|---|---|
| Full-stack TypeScript app | **Next.js 15** (App Router) | Dominant ecosystem, RSC, Server Actions, Vercel synergy |
| Multi-page content site | **Astro** | Zero-JS-by-default, island architecture, best SSG perf |
| Rich SPA, no SSR needed | **React 19** + Vite | Mature, React Compiler auto-memoizes, huge ecosystem |
| Alternative to React | **SvelteKit** or **Nuxt 3** | Smaller bundles, excellent DX, Vue/Svelte syntax |
| Edge-first light app | **Hono** + **htmx** | Tiny runtime, perfect for Cloudflare Workers |

**Default choice: Next.js 15 with App Router** for most production apps. React 19's Server Components, the React Compiler (auto-memoization, 25–40% fewer re-renders), and Server Actions eliminate large categories of boilerplate.

### State Management

Apply the layered model — reach for each layer only when you genuinely need it:

1. **Server state** (API data, fetched content): **TanStack Query v5** — handles caching, background refetch, optimistic updates, pagination. Never put server state in Zustand/Redux.
2. **URL state** (filters, pagination, search): query params via `nuqs` or `next/navigation`
3. **Component-local state**: React `useState` / `useReducer` — the default
4. **Cross-component shared UI state** (theme, modals, drawer): **Zustand** — minimal boilerplate, no providers
5. **Fine-grained reactive atoms**: **Jotai** — ideal when many small independent pieces of state drive different subtrees
6. **Complex business logic with state machines**: **XState v5**

Avoid Redux Toolkit in new projects unless the team already has deep Redux expertise and the app has genuinely complex global state with time-travel debugging needs.

### Backend Framework

| Language | Lightweight/Edge | Full-featured |
|---|---|---|
| TypeScript/Node | **Hono** (Cloudflare Workers, Bun, Node) | **Fastify** (high perf, schema validation) |
| TypeScript/Node | **tRPC** (type-safe RPC, no REST layer) | **NestJS** (enterprise DI, opinionated) |
| Python | **FastAPI** (async, auto OpenAPI docs) | **Django** + DRF (batteries-included) |
| Go | **Gin** or **Echo** (fast, minimal) | Standard library + chi router |

**For Next.js apps**: prefer **tRPC** for internal API calls (end-to-end type safety, zero boilerplate) and **REST/OpenAPI** for public APIs consumed by third parties. Use **Server Actions** for simple mutations that don't need an explicit HTTP contract.

### Database and ORM

**PostgreSQL** is the default choice for nearly all production apps — ACID transactions, JSONB, full-text search, pgvector, Row Level Security, mature ecosystem.

**ORM selection:**
- **Drizzle ORM**: TypeScript-first, SQL-close syntax, minimal bundle (~7.5kB), zero cold-start overhead — ideal for serverless/edge (Vercel, Cloudflare Workers, AWS Lambda)
- **Prisma**: Schema-first, excellent DX, safe migrations, mature ecosystem — ideal for teams that want maximum abstraction and rapid feature development; accept slightly higher cold-start cost
- **SQLAlchemy** (Python): battle-tested ORM + Core for raw SQL when needed

**When to use other databases:**
- **Redis / Upstash**: sessions, rate-limiting counters, pub/sub, ephemeral caches
- **MongoDB**: unstructured documents, variable schema per entity
- **SQLite / Turso / Cloudflare D1**: embedded/edge deployments, local dev, small apps
- **ClickHouse / TimescaleDB**: analytics, time-series, event pipelines

### API Design

**REST**: default for public APIs. Apply these conventions rigorously:
- Noun-based resource URLs: `GET /users/:id/posts`, not `GET /getUserPosts`
- HTTP semantics: GET (idempotent read), POST (create), PUT/PATCH (update), DELETE (remove)
- Consistent error envelope: `{ error: { code, message, details } }`
- Versioning: path prefix `/v1/` or `Accept` header
- Pagination: cursor-based for large datasets; never offset-only at scale

**tRPC**: for TypeScript full-stack apps with shared codebase. Benefits: zero codegen, end-to-end type safety, procedure-level middleware, automatic input validation via Zod.

**GraphQL**: justified when clients have highly variable, complex data requirements (dashboards, mobile + web with different shapes). Use **Pothos** (code-first) or **graphql-yoga**. Avoid N+1 with **DataLoader**.

**gRPC**: internal service-to-service communication at scale; not for browser clients without grpc-web.

---

## Implementation Patterns

### Next.js App Router Architecture

Structure your Next.js 15 project for scale:

```
src/
  app/                    # App Router: pages, layouts, route handlers
    (auth)/               # Route group: no URL impact, shared layout
    (dashboard)/
      layout.tsx          # Persistent dashboard shell
      page.tsx            # Server Component by default
    api/                  # Route Handlers (REST endpoints for external consumers)
  components/
    ui/                   # Primitive components (Button, Input, Modal)
    features/             # Feature-specific composed components
  lib/
    db/                   # Database client (Drizzle/Prisma instance)
    auth/                 # Auth helpers
    validations/          # Shared Zod schemas
  server/
    actions/              # Server Actions grouped by domain
    queries/              # Reusable server-side data fetching functions
  trpc/                   # tRPC router, procedures, context
```

**Server vs. Client Components — the rule of thumb:**
- Default to Server Components. They fetch data, have no JS bundle cost, and can access secrets.
- Add `"use client"` only when you need: event handlers, `useState`/`useEffect`, browser APIs, or third-party client-only libraries.
- Keep the client boundary as low in the tree as possible. Pass Server Component children as props through Client Component wrappers.

**Data fetching patterns:**
```typescript
// Server Component: fetch directly, no useEffect
async function ProductPage({ params }: { params: { id: string } }) {
  const product = await db.query.products.findFirst({
    where: eq(products.id, params.id),
  });
  return <ProductDetail product={product} />;
}

// Parallel fetches with Promise.all to avoid waterfall
const [product, reviews] = await Promise.all([
  getProduct(id),
  getReviews(id),
]);

// Streaming with Suspense for non-critical sections
<Suspense fallback={<ReviewsSkeleton />}>
  <Reviews productId={id} />
</Suspense>
```

### TypeScript Best Practices

- **Strict mode always**: `"strict": true` in `tsconfig.json` — non-negotiable in production code
- **Zod for runtime validation**: Define a single Zod schema; derive the TypeScript type from it with `z.infer<typeof schema>`. Validate at API boundaries, never trust raw request bodies.
- **`satisfies` operator**: Use `const config = { ... } satisfies Config` to get type checking without widening the type
- **Discriminated unions over boolean flags**: `type State = { status: "loading" } | { status: "error"; error: Error } | { status: "success"; data: Data }` — exhaustive `switch` statements catch unhandled cases at compile time
- **No implicit `any`**: If you need to escape the type system, use `unknown` and narrow explicitly
- **Path aliases**: Configure `@/` to map to `src/` — eliminate fragile `../../..` imports
- **Type-only imports**: Use `import type { Foo }` to keep runtime bundle clean

### Authentication and Authorization

**Selection guide (2025–2026):**
- **Clerk**: Best-in-class DX, pre-built UI, Next.js App Router integration, MFA, org management. Pay per MAU — ideal to validate product-market fit quickly.
- **Better Auth**: Open-source, self-hosted or cloud, succeeded Auth.js/NextAuth as the recommended library for projects that want control without Clerk pricing. Full TypeScript support, plugin ecosystem.
- **Supabase Auth**: Ideal if already on Supabase. Tight RLS integration means authorization logic lives in the database layer.
- **Auth.js (NextAuth v5)**: Still maintained for security patches; new projects should prefer Better Auth.

**Authorization patterns:**
```typescript
// Middleware-based route protection (Next.js + Clerk)
export default clerkMiddleware((auth, req) => {
  if (isProtectedRoute(req)) auth().protect();
});

// Row Level Security (PostgreSQL) — authorization at database layer
CREATE POLICY "users can only read own data"
  ON profiles FOR SELECT
  USING (auth.uid() = user_id);

// RBAC in tRPC middleware
const requireRole = (role: Role) =>
  t.middleware(async ({ ctx, next }) => {
    if (!ctx.user?.roles.includes(role)) throw new TRPCError({ code: "FORBIDDEN" });
    return next();
  });
```

**Always:**
- Validate permissions server-side for every API call — never rely on client-side hiding
- Use short-lived access tokens (15 min) + refresh tokens (7–30 days)
- Store tokens in `httpOnly` cookies, not `localStorage`
- Implement CSRF protection for cookie-based auth
- Use PKCE for OAuth flows

### Real-Time Features

**WebSockets**: bidirectional, persistent — best for collaborative features (multiplayer, live cursors, chat). Use **Socket.io** for managed rooms/namespaces or raw WebSocket API with **Partykit**/**Cloudflare Durable Objects** for edge-native.

**Server-Sent Events (SSE)**: server-push only, auto-reconnect — ideal for live notifications, dashboards, AI streaming responses. Simpler than WebSockets; works over HTTP/2.

**AI streaming with SSE:**
```typescript
// Route handler streaming AI response
export async function POST(req: Request) {
  const stream = await anthropic.messages.stream({ /* ... */ });
  return new Response(
    new ReadableStream({
      async start(controller) {
        for await (const chunk of stream) {
          controller.enqueue(new TextEncoder().encode(`data: ${JSON.stringify(chunk)}\n\n`));
        }
        controller.close();
      },
    }),
    { headers: { "Content-Type": "text/event-stream" } }
  );
}
```

---

## Performance Optimization

### Core Web Vitals Targets (2025)
- **LCP (Largest Contentful Paint)**: ≤ 2.5s
- **INP (Interaction to Next Paint)**: ≤ 200ms (replaced FID in 2024)
- **CLS (Cumulative Layout Shift)**: ≤ 0.1

### Frontend Performance Checklist

- [ ] Use `next/image` with `priority` on above-the-fold images; always set explicit `width`/`height`
- [ ] Preload critical fonts with `<link rel="preload">` and use `font-display: swap`
- [ ] Code-split with `next/dynamic` / React `lazy()` for routes and large components
- [ ] Analyze bundle with `@next/bundle-analyzer`; eliminate heavy dependencies
- [ ] Serve static assets with immutable cache headers (`Cache-Control: public, max-age=31536000, immutable`)
- [ ] Use `<link rel="prefetch">` for likely next navigations
- [ ] Avoid layout shifts: reserve space for dynamic content, skeleton screens during loading

### Backend Performance Checklist

- [ ] Index foreign keys and frequently-filtered columns; use `EXPLAIN ANALYZE` to verify query plans
- [ ] Use cursor-based pagination for large tables — never `OFFSET` at scale
- [ ] Cache expensive queries with Redis/Upstash (stale-while-revalidate pattern)
- [ ] Use connection pooling (PgBouncer, Neon connection pooling) — especially critical for serverless
- [ ] Implement HTTP caching headers on API responses where data doesn't change per-user
- [ ] Batch database queries with `DataLoader` pattern to eliminate N+1
- [ ] Offload background jobs (email, image processing, webhooks) to a queue (BullMQ, Inngest, Trigger.dev)

### Caching Strategy

```
Browser Cache (immutable static assets)
    ↓
CDN Edge Cache (Vercel/Cloudflare — public responses, ISR pages)
    ↓
Application Cache (Redis — session data, expensive queries, rate-limit counters)
    ↓
Database Query Cache (connection-level, index-covered queries)
    ↓
Database (source of truth)
```

For AI workloads: implement **prompt caching** (50–90% cost reduction on repeated context) and **semantic caching** (vector-similarity lookup before hitting the LLM) to cut inference costs by 40–60%.

---

## Security

### OWASP Top 10 (2025) — Mitigation Checklist

1. **Broken Access Control** (#1): Enforce server-side authorization on every endpoint. Apply principle of least privilege. Use PostgreSQL RLS for row-level isolation.
2. **Cryptographic Failures**: HTTPS everywhere; never log secrets/tokens; use `bcrypt`/`argon2` for passwords (never MD5/SHA1); rotate secrets regularly.
3. **Software Supply Chain Failures** (#3 in 2025): Pin dependency versions; use `npm audit`/`pnpm audit` in CI; enable Dependabot/Renovate; verify checksums for critical packages.
4. **Injection (SQLi, XSS)**: Use parameterized queries / ORM — never string-concatenate SQL; sanitize and escape all user output; use a CSP header.
5. **Security Misconfiguration**: Remove default credentials; disable directory listing; use security headers middleware.
6. **Vulnerable and Outdated Components**: Automated dependency scanning in CI pipeline.
7. **Identification and Authentication Failures**: MFA support; account lockout after N failures; secure session invalidation on logout.
8. **Data Integrity Failures**: Verify webhook signatures (HMAC); sign JWTs with RS256 for public key verification.
9. **Security Logging and Monitoring Failures**: Log all auth events, admin actions, and anomalies; alert on unusual patterns.
10. **Mishandling of Exceptional Conditions** (new 2025): Never expose stack traces to clients; catch all errors; fail securely (deny by default).

### HTTP Security Headers (apply via middleware)

```typescript
// Next.js middleware or Hono/Fastify plugin
const securityHeaders = {
  "X-Frame-Options": "DENY",
  "X-Content-Type-Options": "nosniff",
  "Referrer-Policy": "strict-origin-when-cross-origin",
  "Permissions-Policy": "camera=(), microphone=(), geolocation=()",
  "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline'; ...",
  "Strict-Transport-Security": "max-age=63072000; includeSubDomains; preload",
};
```

### Additional Security Practices

- **Rate limiting**: Implement at the edge (Cloudflare, Vercel middleware) and at the API layer (using Redis sliding window or token bucket). Default: 100 req/min per IP for public endpoints.
- **Input validation**: Validate with Zod at every API boundary; reject unknown fields with `.strict()`
- **CORS**: Whitelist specific origins; never use `*` for authenticated endpoints
- **Environment secrets**: Use secret managers (Vercel env vars, AWS Secrets Manager, Doppler) — never commit `.env` files
- **SQL injection**: ORM parameterization handles this, but audit any raw query usage
- **Dependency audit**: Run `pnpm audit --audit-level=high` in CI; block deploys on critical vulnerabilities

---

## Testing Strategy

### The Modern Testing Stack (2025–2026)

**Vitest** (unit + integration) + **Playwright** (E2E) is the dominant combination:
- Vitest: 5–10x faster than Jest, Vite config sharing, native ESM, built-in TypeScript support
- Playwright: replaced Cypress as E2E standard — better multi-tab support, lower flakiness, cross-browser

### Test Pyramid for Full-Stack Apps

```
         /\
        /  \    E2E Tests (Playwright)
       /    \   20–30 critical user journeys
      /------\
     /        \  Integration Tests (Vitest)
    /          \  API routes, DB queries, Server Actions
   /------------\
  /              \  Unit Tests (Vitest)
 /                \  Pure functions, utilities, business logic
/__________________\
```

### Unit Tests — what to test

```typescript
// Test pure business logic, not implementation details
describe("calculateDiscount", () => {
  it("applies 20% discount for premium users", () => {
    expect(calculateDiscount({ price: 100, userTier: "premium" })).toBe(80);
  });
});

// Test Server Actions with mocked DB
vi.mock("@/lib/db", () => ({ db: mockDb }));
```

### Integration Tests — API routes and data access

```typescript
// Test the full request/response cycle
const { GET } = await import("@/app/api/products/route");
const req = new Request("http://localhost/api/products?category=shoes");
const res = await GET(req);
expect(res.status).toBe(200);
const data = await res.json();
expect(data.products).toHaveLength(3);
```

### E2E Tests — critical user journeys only

Focus Playwright tests on paths where failure costs money:
- Sign-up / sign-in flow
- Checkout / payment flow
- Core CRUD operations of the product
- Email verification

```typescript
test("user can complete checkout", async ({ page }) => {
  await page.goto("/products/123");
  await page.click('[data-testid="add-to-cart"]');
  await page.goto("/checkout");
  await page.fill('[name="email"]', "test@example.com");
  await page.click('[data-testid="place-order"]');
  await expect(page.locator('[data-testid="order-confirmation"]')).toBeVisible();
});
```

### Testing Checklist

- [ ] Minimum 80% unit test coverage on business logic functions
- [ ] API route integration tests for all happy paths and key error cases
- [ ] 20–30 Playwright E2E tests for revenue-critical flows
- [ ] Visual regression tests for critical UI (Playwright screenshots or Chromatic)
- [ ] Test in CI on every PR; block merge on failure
- [ ] Use `MSW` (Mock Service Worker) to mock API calls in component tests

---

## CI/CD Pipelines

### GitHub Actions — Production-Grade Pipeline

```yaml
# .github/workflows/ci.yml
name: CI/CD
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22, cache: pnpm }
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm typecheck
      - run: pnpm test --coverage
      - run: pnpm audit --audit-level=high

  e2e:
    runs-on: ubuntu-latest
    needs: quality
    steps:
      - uses: actions/checkout@v4
      - run: pnpm install --frozen-lockfile
      - run: pnpm exec playwright install --with-deps chromium
      - run: pnpm exec playwright test

  deploy:
    runs-on: ubuntu-latest
    needs: [quality, e2e]
    if: github.ref == 'refs/heads/main'
    steps:
      - run: pnpm run db:migrate  # Run migrations before deploy
      - run: vercel deploy --prod --token=${{ secrets.VERCEL_TOKEN }}
```

### Pipeline Best Practices

- **Parallelism**: Run lint, typecheck, tests in parallel jobs where possible
- **Caching**: Cache `node_modules`, Playwright browsers, Docker layers between runs
- **Secrets**: Store in GitHub Secrets / environment variables; never hardcode
- **Branch protection**: Require passing CI before merging to `main`
- **Database migrations**: Run before deploying new code; always test rollback
- **Canary deploys**: Use Vercel preview deployments for PR review; promote to prod only after approval
- **Monorepo**: Use Turborepo's `--affected` flag to only run tasks for changed packages

---

## Deployment Playbooks

### Platform Selection

| Scenario | Recommended Platform |
|---|---|
| Next.js app, maximize DX | **Vercel** — first-party Next.js support, preview URLs, edge network |
| Full-stack with databases, background jobs | **Railway** — one-click Postgres/Redis, auto-deploys from GitHub |
| Global distribution, cost efficiency | **Fly.io** — deploy containers to 30+ regions, managed Postgres, scale to zero |
| Edge API, Cloudflare ecosystem | **Cloudflare Workers** + D1/KV/R2/Durable Objects |
| Enterprise, compliance, custom infra | **AWS/GCP** with ECS/GKE + RDS + ElastiCache |

### Docker Best Practices

```dockerfile
# Multi-stage build for Node.js
FROM node:22-alpine AS base
RUN corepack enable pnpm

FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
RUN addgroup -g 1001 nodejs && adduser -S nextjs -u 1001
COPY --from=builder --chown=nextjs:nodejs /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
USER nextjs
EXPOSE 3000
CMD ["node", "server.js"]
```

- Always use multi-stage builds; production image should not contain dev dependencies
- Never run containers as root
- Pin base image versions (`node:22.11.0-alpine`, not `node:latest`)
- Scan images with `docker scout` or Trivy in CI

### Database Migrations in Production

1. **Never run destructive migrations without a backup**
2. **Expand/contract pattern**: add column → deploy → backfill → remove old code → drop column (in separate deployments)
3. Use Drizzle's `drizzle-kit push` (dev) and `drizzle-kit migrate` (prod); Prisma's `prisma migrate deploy`
4. Run migrations in CI before deploying application code
5. Test rollback locally before every production migration

---

## Monorepo Management

### When to use a monorepo

Monorepos are justified when you have shared code between multiple packages (e.g., a Next.js web app + a React Native app + shared types + shared UI components). For a single app, a standard project structure is simpler.

### Turborepo + pnpm workspaces (2025 standard)

```
apps/
  web/          # Next.js app
  mobile/       # React Native / Expo
  docs/         # Astro documentation site
packages/
  ui/           # Shared component library
  config/       # Shared ESLint, TypeScript configs
  database/     # Shared Drizzle schema and client
  types/        # Shared TypeScript types
turbo.json
pnpm-workspace.yaml
```

```json
// turbo.json
{
  "$schema": "https://turborepo.com/schema.json",
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": [".next/**", "dist/**"] },
    "test": { "dependsOn": ["^build"] },
    "lint": {},
    "typecheck": {}
  }
}
```

- **Turborepo** for task orchestration and caching — cache hits reduce CI time by 90%+
- **pnpm** as the package manager — faster installs, strict dependency isolation, smaller `node_modules`
- **Nx** when you need advanced code generation, dependency graph visualization, or release management across many packages (10+)
- Enable Turborepo Remote Cache (Vercel or self-hosted) for team-wide CI cache sharing

---

## AI Feature Integration

### Architecture for AI-Powered Features

Modern full-stack apps increasingly incorporate LLM features. Structure them as a dedicated layer:

```
User Request
    ↓
API Route / Server Action
    ↓
Orchestration Layer (prompt construction, tool routing)
    ↓ ↓ ↓
LLM API  Vector DB  Tool Calls (search, DB, external APIs)
    ↓
Streaming Response → Client via SSE
```

### Key Patterns

**Streaming responses** (never wait for full LLM output):
Use the Vercel AI SDK (`ai` package) for unified streaming across providers with `streamText()`, `streamObject()`, and React hooks (`useChat`, `useCompletion`).

**RAG (Retrieval-Augmented Generation)**:
1. Chunk and embed documents at ingest time (OpenAI `text-embedding-3-small` or local models)
2. Store embeddings in pgvector or Pinecone
3. At query time: embed the user query, similarity-search the vector store, inject top-K results into context
4. Use hybrid search (vector + BM25 keyword) for best recall

**Cost optimization**:
- Implement prompt caching for system prompts and repeated context (50–90% cost reduction)
- Cache frequent LLM responses with semantic similarity lookup before hitting the API
- Choose the smallest model that meets quality requirements; reserve large models for complex reasoning
- Set `max_tokens` budgets and stream to avoid timeout on long generations

**Tool use / function calling**:
Define narrow, composable tools. Each tool should do one thing well. Validate tool call arguments with Zod before execution. Return structured data, not prose, from tools.

---

## Edge Computing

### When to use edge functions

- **Auth middleware**: check session cookies and redirect before the request hits origin — eliminates round trips
- **A/B testing / feature flags**: read cookie or header, serve different content, no origin round-trip
- **Geolocation-based routing**: serve region-specific content at the edge
- **Rate limiting**: count at edge before expensive origin processing
- **Response transformation**: add security headers, rewrite URLs, personalize cached pages

### Cloudflare Workers stack

- **Workers**: V8 isolate runtime, <5ms cold start, runs in 300+ PoPs worldwide
- **KV**: eventually-consistent global key-value; excellent for feature flags, config, cached responses
- **Durable Objects**: strongly-consistent single-instance coordination; ideal for WebSocket rooms, rate limiters, real-time collaboration
- **D1**: SQLite-based relational database at the edge; great for low-write, high-read workloads
- **R2**: S3-compatible object storage with zero egress fees

### Hono for edge APIs

```typescript
import { Hono } from "hono";
import { cors } from "hono/cors";
import { rateLimit } from "@/middleware/rate-limit";

const app = new Hono<{ Bindings: CloudflareBindings }>();

app.use("*", cors({ origin: ["https://myapp.com"] }));
app.use("/api/*", rateLimit({ limit: 100, window: 60 }));

app.get("/api/products", async (c) => {
  const products = await c.env.DB.prepare("SELECT * FROM products LIMIT 20").all();
  return c.json(products.results);
});

export default app;
```

---

## Quick-Reference Checklists

### Pre-Launch Checklist

**Performance**
- [ ] Lighthouse score ≥ 90 on all Core Web Vitals
- [ ] All images use next/image or equivalent optimization
- [ ] Bundle analyzer run; no unnecessary large dependencies
- [ ] Database queries reviewed with EXPLAIN ANALYZE; indexes in place
- [ ] Connection pooling configured for production

**Security**
- [ ] Security headers configured (CSP, HSTS, X-Frame-Options)
- [ ] All API endpoints have authentication checks server-side
- [ ] Input validation with Zod on all API boundaries
- [ ] Secrets in environment variable manager, not in code
- [ ] `pnpm audit` passes with no critical/high vulnerabilities
- [ ] Rate limiting on public endpoints
- [ ] HTTPS enforced; HTTP redirects to HTTPS

**Reliability**
- [ ] Error boundaries in React component tree
- [ ] Structured error logging (Sentry, Axiom, or similar)
- [ ] Database backups configured and tested
- [ ] Uptime monitoring configured (Better Uptime, Checkly)
- [ ] Graceful shutdown handling for Node.js processes
- [ ] Health check endpoint at `/api/health`

**CI/CD**
- [ ] All tests passing in CI before deploy
- [ ] Database migrations run before app deploy
- [ ] Preview deployment for every PR
- [ ] Rollback plan documented

### Code Review Checklist

- [ ] No `any` type escapes without explanation
- [ ] All user input validated before use
- [ ] No secrets in code or logs
- [ ] Server-side authorization on new API routes
- [ ] New database queries have appropriate indexes
- [ ] Tests added for new business logic
- [ ] No N+1 queries introduced
- [ ] Error cases handled (not just happy path)
