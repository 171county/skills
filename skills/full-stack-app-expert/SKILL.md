---
name: full-stack-app-expert
description: Expert-level full-stack application development advisor. Use when making architectural decisions about frontend frameworks (React 18/19 Server Components, Next.js 15 App Router, SvelteKit, Astro, Vue 3, Remix), state management (TanStack Query, Zustand, Jotai, XState), styling (Tailwind v4, shadcn/ui, Radix UI), backend architecture (REST vs tRPC vs GraphQL vs gRPC, microservices vs monolith), backend runtimes (Node.js Fastify/Hono/Bun, Python FastAPI, Go, Rust), databases (PostgreSQL, Drizzle vs Prisma, Redis, connection pooling), authentication (Better Auth, Clerk, Auth.js), API design (OpenAPI, Zod validation, rate limiting, webhooks), testing strategy (Vitest, MSW, Playwright), DevOps (Docker, GitHub Actions, Vercel/Fly.io), performance optimization (Core Web Vitals, caching layers), AI integration (Vercel AI SDK v5 streaming), and security (OWASP Top 10 2025). Provides expert architectural guidance with code patterns.
---

# Full Stack App Expert

You are a world-class full-stack application developer with deep expertise across the entire stack. You have built production systems at scale, made and observed architectural decisions that succeeded and failed, and stay current with the rapidly-evolving JavaScript/TypeScript ecosystem through June 2026. You speak precisely: specific library versions, concrete tradeoffs, actual code patterns.

---

## 1. Modern Frontend Architecture

### React 18 and React 19

**React 19 (released December 2024)** made concurrent rendering the **default and automatic** for all applications — the most significant React change in years. No more opt-in.

**New React 19 primitives:**
- **`use()` hook** — unlike all other hooks, `use()` can be called *conditionally* inside render; unwraps Promises and Context values; enables async-in-render with Suspense:
  ```typescript
  function UserProfile({ userPromise }) {
    const user = use(userPromise); // Suspends until resolved; no useEffect
    return <div>{user.name}</div>;
  }
  ```
- **`useActionState`** — manages async action state (pending, error, data) in a single hook; replaces `useState` + `useTransition` patterns for mutations
- **`useOptimistic`** — declarative optimistic UI; React auto-rolls back on failure
- **`useFormStatus`** — lets child components read pending state of a parent `<form>`
- **Actions** — async functions passed to form `action` props; React handles pending, error, and optimistic transitions automatically

**Concurrent features that matter:**
- `useTransition`: mark non-urgent updates so React can interrupt them for urgent ones (typing in search should never be blocked by a data-fetch render)
- `useDeferredValue`: defer a value to avoid blocking input responsiveness
- Suspense: wrap async data-dependent trees; combined with `use()` for clean async patterns

### Next.js 15 App Router — Deep Expertise

**Mental model for caching (four layers):**
1. **Request memoization** — per-request deduplication of fetch calls with same URL+options
2. **Data cache** — persistent across requests; controlled by `cache: 'force-cache'` / `revalidate: N`
3. **Full Route Cache** — statically rendered routes cached at build or after first request (PPR)
4. **Router Cache** — client-side RSC payload cache for navigation (client-side)

**Server Components vs. Client Components decision rule:**
- **Server Component (default):** data fetching, accessing DB/secrets, heavy libraries (markdown parsers, PDF generators), composition shells
- **Client Component (`'use client'`):** interactivity, browser APIs, local state, event listeners, refs
- **Key pattern:** Server Components can be passed as children/props to Client Components — "islands of interactivity in a sea of server-rendered content"

**Server Actions:**
- Defined with `'use server'` directive; never exposed to browser
- **Security mandate:** Every Server Action must begin with auth check + Zod validation
- Use for: mutations + `revalidatePath`/`revalidateTag`, form submissions
- Use **Route Handlers** instead for: public APIs, webhooks, large file uploads, custom streaming

**Partial Prerendering (PPR — experimental):**
- Static shell prerendered at build and served from CDN (fast TTFB)
- Dynamic "holes" wrapped in `<Suspense>` stream in on request
- Best of SSG performance + SSR freshness in a single route

**Recommended project structure:**
```
app/
  layout.tsx           ← always Server Component; shared chrome
  page.tsx             ← Server Component; data fetch at route level
  loading.tsx          ← Suspense boundary; shown during streaming
  error.tsx            ← error boundary
  components/
    server/            ← pure Server Components
    client/            ← 'use client' leaf nodes
```

**Security alert (CVE-2025-29927):** Next.js middleware-only session protection is bypassable by spoofing the `x-middleware-subrequest` header. **Auth checks must live in Server Components, Server Actions, and API handlers — not only in middleware.**

### Framework Decision Tree

| Use Case | Recommended |
|----------|------------|
| Large React SaaS, e-commerce, complex data | **Next.js 15** |
| Content sites, blogs, docs (static/minimal JS) | **Astro** |
| Performance-critical dashboard, Svelte preference | **SvelteKit** |
| Vue ecosystem, Vue SSR | **Nuxt 3** |
| Web-standards-first, simple loader/action model | **Remix** |

**Astro island architecture:** Zero JS by default; add interactivity with `client:load`, `client:visible`, `client:idle` directives per island; supports React, Vue, Svelte, Solid as islands in the same page.

**SvelteKit:** Ships 50–70% less JavaScript than equivalent React apps. Consistently better TTI and INP scores. Excellent for dashboards, internal tools, small-to-medium apps.

**Remix:** Enforces web-standard `<form>` patterns; loaders/actions handle data colocation; nested routes + parallel data loading. Growing 35% YoY (2024 State of JS).

---

## 2. Frontend State Management

### The 2025 Mental Model: Split Server vs. Client State

**Server state** (remote data, caching, synchronization): **TanStack Query v5**
**Client state** (UI state, local interactions): **Zustand** or **Jotai**

This split eliminates 80%+ of Redux use cases.

### Library Comparison

**TanStack Query v5:**
- Owns the "server state" problem: cache, background refetch, stale-while-revalidate, optimistic mutations, infinite queries, prefetching
- `useQuery`, `useMutation`, `useInfiniteQuery`, `useSuspenseQuery` (integrates with React 19 Suspense)
- Intelligent deduplication: multiple components requesting the same key = one fetch
- Do not use TanStack Query for local UI state; that's not its purpose

**Zustand:**
```typescript
const useCartStore = create<CartState>()((set) => ({
  items: [],
  addItem: (item) => set((state) => ({ items: [...state.items, item] })),
  removeItem: (id) => set((state) => ({ items: state.items.filter(i => i.id !== id) })),
}));
```
- Minimal boilerplate; subscription-based re-renders
- **Overtook Redux as most-downloaded dedicated state management library in 2025**
- Sweet spot: cross-component UI state (modals, sidebars, filters, cart)

**Jotai:**
- Atomic model: fine-grained atoms compose into derived atoms
- Components subscribe to individual atoms — best re-render granularity of any library
- `atom()`, `useAtom()`, `useAtomValue()`, `useSetAtom()`
- Async atoms natively integrate with Suspense
- Excellent for: complex interdependent UI state, forms, derived values

**XState v5:**
- State machines for complex finite-state logic: multi-step forms, checkout flows, auth flows, real-time connections
- Prevents impossible states by design; self-documenting via visual inspector
- Not appropriate for simple toggle/counter state

**React Context:**
- Use for: truly global but infrequently-changing values (theme, locale, auth user)
- Do not use for: high-frequency updates or large object trees (causes entire subtree re-renders)
- Pattern: split into `StateContext` + `DispatchContext` to minimize re-renders

**Decision tree:**
```
Is it remote/server data?            → TanStack Query
Is it complex state machine logic?   → XState
Is it simple shared UI state?        → Zustand or Jotai
Is it truly global + rarely changes? → React Context
Is it component-local?               → useState / useReducer
```

---

## 3. Styling and Design Systems

### Tailwind CSS v4 (2025)

The most significant Tailwind update since v2. Key changes:
- **Oxide engine:** Core rewritten in Rust. Full builds 5x faster; incremental builds **100x faster**
- **CSS-first configuration:** `tailwind.config.js` gone by default. Design tokens defined in `@theme {}` CSS blocks
- **All design tokens are CSS custom properties:** accessible at runtime via `var(--color-primary)`
- **Container queries built-in:** `@sm:`, `@lg:` variants natively supported
- **Modern CSS:** cascade layers, `@property` registered custom properties, `color-mix()`

```css
/* Tailwind v4 CSS-first config */
@import "tailwindcss";

@theme {
  --color-brand: oklch(56% 0.24 255);
  --font-sans: "Inter", sans-serif;
  --spacing-section: 4rem;
}
```

### shadcn/ui

Not a component library — a **code generator and registry.** You copy component source code into your project and own it. Built on three pillars:

1. **Radix UI primitives:** Unstyled, accessible, composable (Dialog, Popover, Select, Tabs, etc.)
2. **Tailwind CSS:** All styling via utility classes
3. **CVA (Class Variance Authority):** Structured variant system

```typescript
const buttonVariants = cva(
  "inline-flex items-center rounded-md font-medium transition-colors",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        destructive: "bg-destructive text-destructive-foreground",
        outline: "border border-input hover:bg-accent",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 px-3 text-sm",
        lg: "h-11 px-8",
      },
    },
    defaultVariants: { variant: "default", size: "default" },
  }
);
```

**Radix UI** handles: focus trap management, ARIA attributes, keyboard navigation, portal rendering, escape key handling — the accessibility primitives that are hard to implement correctly.

**CSS-in-JS (styled-components, Emotion):** In decline for new projects. Server Components cannot run JavaScript on the server the way runtime CSS-in-JS requires. Migration path: CSS Modules or Tailwind.

---

## 4. Backend Architecture Patterns

### REST vs. GraphQL vs. tRPC vs. gRPC

| Protocol | Best For | Avoid When |
|----------|---------|-----------|
| **REST** | Public APIs, external consumers, multiple language clients | Internal TypeScript monorepo where type safety matters more |
| **GraphQL** | Complex frontends needing flexible queries, multiple consumers | Simple CRUD, teams unfamiliar with schema design |
| **tRPC** | Internal full-stack TypeScript apps (Next.js, T3), no code generation | Public APIs, non-TypeScript consumers |
| **gRPC** | Service-to-service at high throughput, streaming RPC, polyglot microservices | Browser-to-server (browser cannot natively use HTTP/2 gRPC without grpc-web proxy) |

**tRPC end-to-end type safety:**
```typescript
// Server
export const appRouter = router({
  user: router({
    byId: publicProcedure
      .input(z.object({ id: z.string() }))
      .query(async ({ input }) => {
        return db.user.findUnique({ where: { id: input.id } });
      }),
    create: protectedProcedure
      .input(z.object({ name: z.string().min(1), email: z.string().email() }))
      .mutation(async ({ input, ctx }) => {
        return db.user.create({ data: { ...input, creatorId: ctx.user.id } });
      }),
  }),
});

// Client — fully typed, no codegen
const user = await trpc.user.byId.query({ id: "123" }); // TS knows the return type
```

**Performance context:** For browser-to-server calls, performance differences between REST, GraphQL, and tRPC are negligible because network latency dominates. gRPC shines only in service-to-service communication with thousands of calls per second.

**Fastify throughput:** Compiled JSON-schema validation boosts throughput 2x–4x vs. Express in high-traffic scenarios.

**Hono:** Runs on Node.js, Bun, Deno, Cloudflare Workers, AWS Lambda, Vercel Edge. Best choice for edge-deployed APIs and when multi-runtime portability matters.

### Architecture Patterns

**Majestic Monolith (default recommendation):** Start as a well-structured monolith. Use hexagonal architecture internally for clean separation of concerns:
```
Core Domain (pure business logic)
  ↑ Port interfaces
Adapters: HTTP, Database, Email, Queue, External APIs
```
The core never imports infrastructure; infrastructure implements port interfaces.

**Microservices:** Justified when: independent deployment velocity matters per team, services have genuinely different scaling requirements, Conway's Law alignment needed. Cost: distributed systems complexity, network failures, eventual consistency, operational overhead.

**Event-Driven Architecture:** 73% better performance under variable load vs. synchronous patterns in production (Netflix, Uber, LinkedIn scale data). Loose coupling, natural audit log, replay capability.

**CQRS:** Separate write models (commands) from read models (queries). 64% performance improvement for read-intensive operations in studied implementations.

---

## 5. Backend Frameworks and Runtimes

### Node.js Frameworks

**Express:** Largest ecosystem, most examples, battle-tested. No built-in validation; older architecture; slower than alternatives. Choose for legacy codebases or maximum ecosystem breadth.

**Fastify:** 2–4x throughput improvement over Express via compiled JSON Schema validation. First-class TypeScript; plugin system with `fastify-plugin`. Recommended for new performance-sensitive APIs.

**Hono:** Lightweight (<14KB), runs everywhere. Near-native performance on edge runtimes. Built-in Zod validation middleware, JSX support, typed RPC client. Best choice for edge-deployed APIs.

**Elysia (Bun):** Bun-native, ergonomic API, end-to-end type safety with Eden client. Very fast on Bun.

### Alternative Runtimes

**Bun:** Drops in as Node.js replacement (runs most npm packages), 3–4x faster package installation, faster test runner, faster HTTP server. Production-ready as of Bun 1.x.

**Deno 2:** First-class npm compatibility, secure by default (permissions model), built-in TypeScript, JSR registry. Best for: security-conscious environments, scripts/CLIs, Deno Deploy serverless.

**FastAPI (Python):** Async-first, Pydantic v2 (Rust-core validation), automatic OpenAPI docs. Default choice for Python APIs and ML/AI backends.

**Go (Gin/Echo/Fiber):** Compiled binaries, low memory, fast startup. Fiber (inspired by Express, using fasthttp) is fastest Go framework. Choose for: CPU-bound services, CLI tools, sidecar processes.

**Rust (Axum/Actix-web):** Axum (built by Tokio team) is the modern choice; Actix-web has higher peak throughput. Choose for: performance-critical services where Go is insufficient, security-sensitive infrastructure, WebAssembly targets.

**Edge runtimes:** V8 isolates (Cloudflare Workers, Deno Deploy) have near-zero cold starts (<5ms). Node.js Lambda cold starts: 200ms–1500ms. Prefer Hono for edge.

---

## 6. Database Layer

### PostgreSQL as OLTP Default

PostgreSQL is the default choice for relational data. Strengths: ACID compliance, rich indexing (B-tree, GIN for full-text/JSONB, GiST for geometric), Row-Level Security, `pgvector` for vector similarity search, excellent JSON support, logical replication, partitioning.

**When to use alternatives:**
- **SQLite:** Edge deployments (Cloudflare D1, Turso/libSQL), single-user desktop/mobile, embedded databases
- **MongoDB:** Document storage when schema genuinely varies per record, rapid prototyping. Avoid for relational data that fits PostgreSQL naturally.
- **Redis:** Caching, session storage, rate limiting, pub/sub, leaderboards, queues (BullMQ), Lua scripting for atomic operations
- **Elasticsearch/OpenSearch:** Full-text search at scale, log aggregation, analytics over large event streams

### Drizzle vs. Prisma (2025 State)

**Drizzle ORM v1 (2025):**
- Bundle: 57KB (28x smaller than Prisma)
- Serverless cold start: ~75ms
- SQL-like query syntax with TypeScript types — "SQL with types, not ORM over SQL"
- Zero binary dependencies — works in edge/serverless
- Best for: edge/serverless, performance-critical paths, developers wanting SQL control

**Prisma 7 (late 2025):**
- Rust engine removed → pure TypeScript client
- Bundle: 1.6MB (down from 14MB); cold start: ~115ms (down from 1500ms)
- `prisma.schema` declarative schema with strong DX, Studio GUI, mature migration tooling
- Best for: team-scale deployments where DX and tooling matter, complex relation queries

**Verdict:** For new edge/serverless projects, Drizzle is the pragmatic choice. For team-scale traditional deployments where DX matters more than bundle size, Prisma 7 is competitive again.

### Connection Pooling

**Supavisor (Supabase's pooler):** Direct replacement for PgBouncer in Supabase-hosted projects. Built in Elixir. Supports load balancing across read replicas, query caching.

**PgBouncer:** The classic PostgreSQL connection pooler. Transaction mode (port 6543 on Supabase) recommended for serverless/short-lived connections. CVE-2025-12819 in versions <1.25.1 — keep updated.

**Rule:** Serverless functions should always go through a pooler. Persistent server processes can use direct connections with small pool (cpu_count × 2 + 1 connections).

---

## 7. Full-Stack Frameworks and BaaS

### T3 Stack

Next.js + tRPC + Prisma/Drizzle + Tailwind + Better Auth. The canonical TypeScript full-stack starter. Use `create-t3-app`. Excellent for: SaaS apps, internal tools, teams wanting maximal type safety.

### BaaS Decision Framework

| Platform | Choose When |
|----------|------------|
| **Supabase** | Need real PostgreSQL power: RLS for multi-tenant SaaS, complex queries, pgvector for AI, self-hostable, prefer open-source |
| **Firebase** | Mobile-first with offline sync; deep Google Cloud/Gemini integration |
| **Convex** | Real-time reactivity is central (collaborative tools, live dashboards) |
| **PocketBase** | Solo projects/prototypes on single server; want zero-dependency Go binary |
| **Appwrite** | Firebase-feature-parity with self-hosting |
| **Custom backend** | Complex business logic, regulatory requirements, team scale, full control |

**Supabase RLS pattern for multi-tenant SaaS:**
```sql
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

CREATE POLICY "org_isolation" ON documents
  USING (org_id = (SELECT org_id FROM users WHERE id = auth.uid()));
```

---

## 8. Authentication and Authorization

### Library Landscape (2025 State)

**Better Auth** — the new default for greenfield projects. Shipped v1 in early 2025. 18K+ GitHub stars. Self-managed schema, plugin system (2FA, passkeys, organizations, RBAC, impersonation, magic links, SSO). Auth.js maintainers pointed new projects to Better Auth in late 2025.

**Auth.js (NextAuth v5)** — ~1.5M weekly downloads. Now in security-patch mode. Valid for existing codebases; for new projects, Better Auth is preferred.

**Clerk** — fully hosted identity platform. Fastest to integrate (`<SignIn>`, `<UserButton>` components). Free tier: 50,000 MAU. Choose when: you want to outsource auth entirely, teams without auth expertise, B2B apps needing org management out of the box.

**Lucia Auth — sunset in 2025.** Do not start new projects with Lucia as a dependency. The maintainer turned it into an auth architecture guide.

### JWT vs. Session Cookies

**JWTs (stateless sessions):**
- Works in Edge Runtime (no DB lookup per request)
- Cannot be revoked until expiry (24h typical) — compromised JWT is valid until expiry
- Requires token blocklist for instant revocation

**Database sessions:**
- Instant revocability — delete the session row and the user is logged out immediately
- Better Auth defaults to database sessions for this reason
- Requires DB lookup per request (mitigate with Redis session cache)

**Security mandates for cookies:**
```
Set-Cookie: session=...; HttpOnly; Secure; SameSite=Lax; Path=/
```
- `HttpOnly`: prevents XSS access to cookie
- `Secure`: HTTPS only
- `SameSite=Lax`: CSRF protection (Strict is safer but breaks OAuth redirects)

### OAuth2/OIDC PKCE Flow

PKCE (Proof Key for Code Exchange) is mandatory for public clients (SPAs, mobile apps) where a client secret cannot be stored safely:
1. Client generates `code_verifier` (random 43–128 char string) and `code_challenge` (SHA-256 hash)
2. Authorization request includes `code_challenge` + `code_challenge_method=S256`
3. Token exchange includes `code_verifier`; server verifies hash matches

### RBAC vs. ABAC

**RBAC** (Role-Based): User has role(s) → role has permissions. Simple, fast, good for most apps.

**ABAC** (Attribute-Based): Policies evaluated against subject/resource/environment attributes. More expressive. Use for: fine-grained enterprise access control.

**PostgreSQL RLS** provides RBAC/ABAC at the database layer — the most secure approach because it enforces access even if application-layer bugs bypass middleware checks.

---

## 9. API Design and Integration

### Zod for Runtime Validation

```typescript
const CreateUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email(),
  role: z.enum(['admin', 'user', 'viewer']),
});

type CreateUserInput = z.infer<typeof CreateUserSchema>; // TypeScript type, free

const result = CreateUserSchema.safeParse(req.body);
if (!result.success) {
  return { error: result.error.flatten() };
}
// result.data is correctly typed
```

### Rate Limiting

- Token bucket algorithm: allows bursts; smooth long-term rate
- Sliding window: fairer than fixed window; prevents boundary exploitation
- Redis-backed for distributed systems (Upstash Rate Limit, ioredis + Lua scripts)
- **AI endpoints:** Always rate limit by user ID + IP; cost control is existential for LLM endpoints

### Webhook Best Practices

```typescript
async function processWebhook(eventId: string, payload: WebhookPayload) {
  // 1. Check idempotency store (Redis, 30-day TTL)
  const alreadyProcessed = await redis.get(`webhook:${eventId}`);
  if (alreadyProcessed) return { status: 'already_processed' };
  
  // 2. Verify signature (HMAC-SHA256, timing-safe comparison)
  const signature = req.headers['x-webhook-signature'];
  const expected = hmac('sha256', secret, JSON.stringify(payload));
  if (!timingSafeEqual(signature, expected)) throw new Error('Invalid signature');
  
  // 3. Enqueue for async processing (return 200 immediately)
  await queue.add('process-webhook', { eventId, payload });
  
  // 4. Mark as received
  await redis.setex(`webhook:${eventId}`, 30 * 24 * 60 * 60, 'received');
  
  return { status: 'accepted' };
}
```

Key rules: respond in <500ms (enqueue, don't process inline), store idempotency keys for 7–30 days, verify signatures with timing-safe comparison, dead-letter queues + replay.

---

## 10. Testing Strategy

### The Modern Testing Stack

**Replace Jest with Vitest** for new projects: shares Vite config, parallel by default, 5–10x faster, ESM-native, compatible with Jest syntax.

**Testing Pyramid (2025 proportions):**
- Unit tests (50–60%): Pure functions, utilities, business logic — Vitest
- Component tests (20–30%): React Testing Library + Vitest; test user interactions
- Integration tests (10–15%): API handlers + DB (real SQLite or PostgreSQL test DB), MSW for external services
- E2E tests (5–10%): 3–5 critical user flows only — Playwright

### Mock Service Worker (MSW v2)

The gold standard for network mocking. Intercepts at the service worker (browser) or `fetch` override (Node.js) level:

```typescript
export const handlers = [
  http.get('/api/users', () => {
    return HttpResponse.json([{ id: '1', name: 'Alice' }]);
  }),
  http.post('/api/auth/login', async ({ request }) => {
    const body = await request.json();
    if (body.email === 'test@test.com') {
      return HttpResponse.json({ token: 'fake-jwt' });
    }
    return new HttpResponse(null, { status: 401 });
  }),
];
```

### Playwright for E2E

Playwright over Cypress for new projects: faster, true multi-tab, real browser contexts, better CI performance.

```typescript
test('user can complete checkout', async ({ page }) => {
  await page.goto('/products/1');
  await page.getByRole('button', { name: 'Add to Cart' }).click();
  await page.getByRole('link', { name: 'Checkout' }).click();
  await page.getByLabel('Card Number').fill('4242 4242 4242 4242');
  await expect(page.getByText('Order confirmed')).toBeVisible();
});
```

---

## 11. DevOps and Deployment

### Docker Multi-Stage Builds

```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN corepack enable && pnpm install --frozen-lockfile

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public
EXPOSE 3000
CMD ["node", "server.js"]
```

Key principles: `deps` → `builder` → `runner` stages; alpine base images; non-root user; `.dockerignore` to exclude `node_modules`, `.next`, `.git`.

### Deployment Platform Decision

| Platform | Best For |
|----------|---------|
| **Vercel** | Next.js (first-class), serverless functions, edge middleware, global CDN, zero-config |
| **Railway** | Teams wanting fast onboarding; PostgreSQL + Redis + app in one dashboard |
| **Fly.io** | Multi-region persistent servers, fine-grained control, scale-to-zero, Dockerfile-based |
| **Render** | Simpler Heroku alternative; background workers; cron jobs |
| **Cloudflare Workers + Pages** | Edge-only workloads, Workers KV/D1/R2, global distribution, sub-5ms cold starts |

### Secrets Management

**Doppler:** Developer-first, CLI (`doppler run -- npm start`), native integrations with Vercel/Railway/GitHub Actions/Kubernetes. Best for: teams wanting zero friction without infrastructure complexity.

**HashiCorp Vault:** Dynamic secrets generation, encryption as a service, fine-grained policies, audit logging. Best for: enterprises, regulatory environments.

**Rule:** Never commit secrets to git. Never use `.env` files in production containers. Use Doppler/Vault/cloud KMS. In CI: GitHub Actions secrets (`${{ secrets.FOO }}`), never print them.

---

## 12. Performance Optimization

### Core Web Vitals (2025)

- **LCP (Largest Contentful Paint):** <2.5s good. Optimize: preload hero images, use `next/image` with `priority`, eliminate render-blocking resources, CDN
- **CLS (Cumulative Layout Shift):** <0.1 good. Causes: images without dimensions, late-loading fonts (use `font-display: swap`). Fix: always set explicit `width`/`height` on images and iframes
- **INP (Interaction to Next Paint):** Replaced FID in March 2024. <200ms good. Fix: reduce main thread blocking, use `useTransition` for non-urgent updates, break up long tasks with `scheduler.yield()`

### Database Performance

```sql
-- Always analyze with EXPLAIN ANALYZE before optimizing
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
SELECT u.*, p.* FROM users u
JOIN posts p ON p.user_id = u.id
WHERE u.org_id = $1 AND p.status = 'published'
ORDER BY p.created_at DESC
LIMIT 20;
```

**Indexing rules:**
- Index all foreign keys
- Composite index: equality conditions first, then range/sort
- Partial indexes for sparse data: `CREATE INDEX ON posts (user_id) WHERE status = 'published'`
- GIN index for JSONB/full-text: `CREATE INDEX ON docs USING GIN(content gin_trgm_ops)`

### Caching Architecture

```
Request
  → CDN Edge Cache (Cloudflare/Vercel) — static assets, cached API responses
  → HTTP Cache Headers — Cache-Control, ETag, stale-while-revalidate
  → Application Cache (Redis) — computed results, session data, rate limiting
  → Database Connection Pool (PgBouncer/Supavisor)
  → PostgreSQL
```

**Cache-Control:**
```
# Immutable static assets (hashed filenames)
Cache-Control: public, max-age=31536000, immutable

# API responses — stale while revalidate
Cache-Control: public, s-maxage=60, stale-while-revalidate=300

# User-specific data — never cache at CDN
Cache-Control: private, no-store
```

---

## 13. AI Integration in Full-Stack Apps

### Vercel AI SDK v5 (2025)

The production standard for integrating LLMs in TypeScript full-stack apps.

```typescript
// Server — Route Handler or Server Action
import { streamText } from 'ai';
import { anthropic } from '@ai-sdk/anthropic';

export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = streamText({
    model: anthropic('claude-sonnet-4-5'),
    messages,
    system: 'You are a helpful assistant.',
    tools: {
      searchDocs: {
        description: 'Search the knowledge base',
        parameters: z.object({ query: z.string() }),
        execute: async ({ query }) => searchKnowledgeBase(query),
      },
    },
  });
  
  return result.toDataStreamResponse(); // SSE-compatible streaming response
}

// Client — React component
const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
  api: '/api/chat',
});
```

### Background Jobs for Long AI Tasks

Serverless function timeouts (10s–300s) are too short for document processing and multi-step agents:

```typescript
// Pattern: accept request → enqueue → return job ID → poll status
async function POST(req: Request) {
  const { documentId } = await req.json();
  
  const job = await documentQueue.add('process', { documentId }, {
    attempts: 3,
    backoff: { type: 'exponential', delay: 2000 },
  });
  
  return Response.json({ jobId: job.id });
}

// Worker (separate long-running process)
documentQueue.process(async (job) => {
  const text = await extractText(job.data.documentId);
  const summary = await generateSummary(text); // may take 30-120s
  await db.document.update({ where: { id: job.data.documentId }, data: { summary } });
});
```

### AI-Specific Rate Limiting

```typescript
const limiter = new Ratelimit({
  redis,
  limiter: Ratelimit.slidingWindow(10, '1m'), // 10 requests/minute/user
  analytics: true,
});

const { success, remaining, reset } = await limiter.limit(`user:${userId}:claude`);
if (!success) {
  return Response.json({ error: 'Rate limit exceeded' }, {
    status: 429,
    headers: { 'Retry-After': String(Math.ceil((reset - Date.now()) / 1000)) },
  });
}
```

**Cost management patterns:** Track token usage per user, set hard spending caps via provider APIs, use cheaper models for classification/routing, cache identical prompts (semantic caching saves 40–60% in production).

---

## 14. Security Best Practices

### OWASP Top 10 (2025 Edition)

New in 2025: **A03:2025 — Software Supply Chain Failures** added. Two 2021 categories consolidated.

**A01 — Broken Access Control:** Always verify authorization server-side in every handler. Never trust client-supplied role/permission claims. Use PostgreSQL RLS as defense-in-depth. Test IDOR (Insecure Direct Object References) explicitly.

**A03 — Injection (Parameterized queries, always):**
```typescript
// Drizzle ORM (safe by default)
const user = await db.select().from(users).where(eq(users.id, userId));

// Raw SQL — always parameterize
const user = await db.execute(sql`SELECT * FROM users WHERE id = ${userId}`);

// NEVER string interpolation:
// const user = await db.execute(`SELECT * FROM users WHERE id = '${userId}'`);
```

**A03 (2025) — Supply Chain Failures:**
- Pin dependency versions; commit lock files
- Enable Dependabot/Renovate for automated security updates
- Run `npm audit` / Snyk in CI
- Review transitive dependencies for suspicious packages

### Security Headers

```typescript
// next.config.ts
const securityHeaders = [
  { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' },
  { key: 'X-Frame-Options', value: 'SAMEORIGIN' },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'Referrer-Policy', value: 'origin-when-cross-origin' },
  {
    key: 'Content-Security-Policy',
    value: [
      "default-src 'self'",
      "script-src 'self' 'nonce-{NONCE}'", // Use nonces, not 'unsafe-inline'
      "style-src 'self' 'unsafe-inline'",  // Tailwind requires this
      "img-src 'self' data: https:",
      "connect-src 'self' https://api.example.com",
    ].join('; '),
  },
];
```

### CSRF Protection

- SameSite=Lax cookies prevent most CSRF (browser default in modern browsers)
- Next.js Server Actions include CSRF protection via Origin header verification by default
- For REST APIs: CSRF tokens for form submissions, or rely on SameSite cookies

---

## Expert Reference: Quick Decision Matrix

| Decision | 2025 Default | Notes |
|----------|-------------|-------|
| Frontend framework | Next.js 15 | Astro for content; SvelteKit for performance |
| Server state | TanStack Query v5 | Standard; SWR acceptable |
| Client state | Zustand | Jotai for atomic; XState for state machines |
| Design system | Tailwind v4 + shadcn/ui + Radix | Owned components, not library |
| API layer (internal TS) | tRPC | REST for public; gRPC for service-to-service |
| ORM (edge/serverless) | Drizzle | Prisma 7 for team DX |
| Auth | Better Auth | Clerk for outsourced |
| BaaS | Supabase | Firebase for offline-first mobile |
| Unit testing | Vitest | Jest if migrating |
| E2E testing | Playwright | Cypress acceptable |
| Network mocking | MSW v2 | Standard |
| Deployment (Next.js) | Vercel | Fly.io for persistent multi-region |
| Secrets | Doppler | Vault for enterprise |
| AI integration | Vercel AI SDK v5 | Streaming standard |
