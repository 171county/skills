---
name: full-stack-app-expert
description: Expert-level advisor on full-stack application development. Use this skill whenever the user asks about frontend frameworks (Next.js 15/16, Astro, SvelteKit, Remix/React Router 7), backend architecture (REST, tRPC, GraphQL, serverless), database selection and schema design (Supabase, Neon, PlanetScale, Turso, Redis), AI-native application patterns (SSE streaming, Vercel AI SDK, WebSockets for agents), authentication (Clerk, Better Auth, Auth.js v5), file storage (Cloudflare R2, Vercel Blob, S3), deployment and hosting (Vercel, Railway, Fly.io, Render, AWS), TypeScript monorepos (Turborepo, Drizzle ORM, Zod), state management (TanStack Query, Zustand, Jotai), testing (Vitest, Playwright, MSW), performance optimization (Core Web Vitals, RSC, PPR), mobile and cross-platform development (React Native + Expo, Tauri 2.x), security (OWASP Top 10:2025, CSP, CORS), or AI-assisted development workflow (Cursor, Claude Code).
---

# Full-Stack App Expert

You are an expert-level full-stack application developer. Give precise, production-grade technical guidance. All information current as of June 2026.

---

## 1. Frontend Framework Decision Matrix

### Next.js 15 / 16 (Dominant Choice, 2026)
**Market position**: 57% of Next.js adoption for new projects (2025 State of JS). Best choice when you need SSR, SSG, ISR, or RSC with a single framework.

**Key capabilities (Next.js 15/16)**:
- **Turbopack** (default in Next.js 15): Webpack replacement with 50-60% faster cold starts, 90% faster HMR
- **Partial Prerendering (PPR)**: Static shell + streaming dynamic content — eliminates the static vs. dynamic tradeoff
- **React Server Components (RSC)**: Default in App Router; zero JavaScript shipped to client for server components
- **Server Actions**: Type-safe server mutations without API routes
- **`next/after`**: Execute post-response tasks (analytics, caching) after response is sent to client

**Use Next.js when**: Full-stack React app, e-commerce, marketing site with dynamic content, enterprise SaaS frontend, or any app where SEO + interactivity coexist.

**Avoid when**: Pure static site (Astro is better), non-React team, heavy real-time requirements (add a WebSocket layer).

### Astro (Post-Cloudflare Acquisition, January 2026)
**Cloudflare acquired Astro** in January 2026. Astro is now the official framework for Cloudflare Pages. Deep integration with Workers, D1, KV, R2, and Durable Objects.

**Islands architecture**: Zero JavaScript by default; hydrate specific islands with any UI framework (React, Vue, Svelte, Preact). Ideal for content-heavy sites with selective interactivity.

**Use Astro when**: Documentation sites, blogs, marketing pages, content-heavy apps where JavaScript bundle size is critical. Now the default recommendation for Cloudflare deployments.

### SvelteKit
**Developer satisfaction**: 88% (State of JS 2025) — highest of any framework. Svelte compiles to vanilla JavaScript, no virtual DOM overhead.

**Use SvelteKit when**: Performance is paramount, team is comfortable with non-React ecosystem, smaller bundle is critical, PWA development.

### Remix / React Router 7 (RR7)
Remix and React Router merged into **React Router v7** (2025). Unified routing, loader/action API, and Vite-native build system.

**Use RR7 when**: Existing React Router 6 codebase upgrading, data-loading co-location is a priority, progressive enhancement is required (works without JS).

---

## 2. Backend Architecture Patterns

### REST + OpenAPI
**When to use**: Public APIs, third-party integrations, mobile clients, polyglot environments. Standard for anything external-facing.

**2026 stack**: FastAPI (Python) or Hono (TypeScript/Cloudflare Workers) + Zod/Pydantic for validation + TypeSpec for schema-first API design. Generate client SDKs from OpenAPI spec with `openapi-typescript` or `orval`.

### tRPC (TypeScript Monorepos)
End-to-end type safety with zero code generation. Router procedures defined on the server are automatically typed on the client.

**Use tRPC when**: TypeScript monorepo where frontend and backend are co-located. Full-stack Next.js apps are the perfect tRPC use case.

```typescript
// Server: define procedure
export const appRouter = router({
  user: router({
    getById: publicProcedure
      .input(z.object({ id: z.string() }))
      .query(async ({ input }) => {
        return db.user.findUnique({ where: { id: input.id } });
      }),
  }),
});

// Client: fully typed, no code generation
const user = await trpc.user.getById.query({ id: "123" });
//    ^-- Typed as User | null automatically
```

### GraphQL
**When to use**: Multiple clients (web + mobile + third-party) with different data needs. Product teams querying complex relational data. High frontend flexibility requirement.

**2026 stack**: Pothos (type-safe schema builder for TypeScript) + Apollo Client or urql. Consider **GraphQL Yoga** for serverless-compatible server. GraphQL Mesh for unified graph across multiple APIs.

**Avoid GraphQL when**: Simple CRUD app, single client, small team. The tooling overhead rarely pays off below 3+ client types.

### gRPC
**When to use**: Internal microservice-to-microservice communication, high-throughput binary data exchange, strongly typed contracts between services. Not for browser clients (requires gRPC-Web or Connect RPC).

**2026**: **Connect RPC** (Buf) is replacing gRPC for new projects — HTTP/1.1 and HTTP/2 compatible, browser-native, TypeScript-first.

---

## 3. Database Selection

### Decision Framework

| Requirement | Database | Why |
|---|---|---|
| Managed Postgres + auth + storage | **Supabase** | RLS, realtime, storage, edge functions in one platform |
| Serverless Postgres (branching) | **Neon** | Git-like branch-per-PR databases; scale to zero |
| Serverless MySQL (horizontal scale) | **PlanetScale** | Vitess-based; schema changes with no downtime |
| Edge-native SQLite | **Turso** | LibSQL; closest-region serving, per-tenant databases |
| Redis-compatible cache/queue | **Upstash** | Serverless Redis; HTTP API for edge |
| Postgres (existing team) | **pgvector + Supabase** | Add vector search to existing Postgres |
| Billion-scale vector search | **Milvus** or **Qdrant** | Purpose-built vector DBs |

### Supabase (2026 Standard for Startups)
**Row Level Security (RLS)**: Define access policies in SQL that enforce at the database layer — works with any ORM or raw queries.

```sql
-- Only users can read their own data
CREATE POLICY "users_own_data" ON profiles
  FOR ALL USING (auth.uid() = user_id);
```

**Supabase realtime**: Subscribe to database changes via WebSocket. Used for live dashboards, collaborative features, notifications.

**Supabase Edge Functions**: Deno-based serverless functions co-located with the database. Ideal for webhooks and database triggers.

### Drizzle ORM (2026 Default)
Drizzle is now the dominant TypeScript ORM for production applications (surpassed Prisma in TS community adoption, 2025).

```typescript
import { pgTable, serial, text, timestamp } from "drizzle-orm/pg-core";
import { drizzle } from "drizzle-orm/postgres-js";

const users = pgTable("users", {
  id: serial("id").primaryKey(),
  email: text("email").notNull().unique(),
  createdAt: timestamp("created_at").defaultNow()
});

const db = drizzle(connectionString);

// Type-safe queries
const user = await db.select().from(users).where(eq(users.email, "alice@example.com"));
```

**Why Drizzle over Prisma**: Lighter bundle (critical for edge), SQL-first mental model, better Cloudflare Workers/Edge support, no query engine binary, faster query generation.

---

## 4. AI-Native Application Patterns

### Vercel AI SDK (Production Standard)
The dominant library for AI-native full-stack apps. Works with Next.js, SvelteKit, Nuxt, and plain Node.

**Core patterns**:

```typescript
import { streamText, generateObject } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { z } from "zod";

// Streaming text response
const { textStream } = streamText({
  model: anthropic("claude-opus-4-8"),
  messages: [{ role: "user", content: userMessage }],
  tools: {
    get_weather: {
      description: "Get weather for a location",
      parameters: z.object({ location: z.string() }),
      execute: async ({ location }) => fetchWeather(location)
    }
  }
});

// Structured output generation
const { object } = await generateObject({
  model: anthropic("claude-sonnet-4-6"),
  schema: z.object({
    sentiment: z.enum(["positive", "negative", "neutral"]),
    confidence: z.number().min(0).max(1),
    summary: z.string()
  }),
  prompt: "Analyze this review: " + review
});
```

**`useChat` hook** (React):
```typescript
const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
  api: "/api/chat",
  onError: (error) => toast.error(error.message)
});
```

### SSE for Streaming (Standard Pattern)
Server-Sent Events are the standard for token streaming from AI endpoints to browsers:

```typescript
// Next.js API Route
export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = streamText({
    model: anthropic("claude-sonnet-4-6"),
    messages
  });
  
  return result.toDataStreamResponse(); // Vercel AI SDK SSE response
}
```

**SSE advantages**: Works over standard HTTP (CDN-compatible), reconnects automatically, no WebSocket handshake overhead. Use SSE for unidirectional streaming (server → client).

### WebSockets for Agent Sessions
Use WebSockets when the client needs to **interrupt** the agent, send follow-up messages, or cancel generation:

```typescript
// WebSocket handler for agentic chat with interruption
ws.on("message", async (data) => {
  const { type, payload } = JSON.parse(data);
  if (type === "cancel") return agent.abort();
  if (type === "message") agent.send(payload);
});
```

---

## 5. Authentication

### Clerk (Managed, Enterprise-Ready)
Fully managed auth with pre-built UI components. Handles OAuth, magic links, MFA, SAML SSO, and bot protection.

**Use Clerk when**: You want zero auth maintenance overhead, need enterprise SSO (SAML, OIDC), or your users expect polished auth UI. $25/month → enterprise pricing.

```typescript
// Next.js App Router with Clerk
import { auth } from "@clerk/nextjs/server";

export async function GET() {
  const { userId } = await auth();
  if (!userId) return new Response("Unauthorized", { status: 401 });
  // ...
}
```

### Better Auth (Open-Source, 2026 Default)
Emerged as the top open-source auth library for TypeScript (surpassed NextAuth/Auth.js in community adoption by mid-2025). Framework-agnostic, database-agnostic, edge-compatible.

```typescript
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";

export const auth = betterAuth({
  database: drizzleAdapter(db, { provider: "pg" }),
  socialProviders: {
    github: { clientId: env.GITHUB_CLIENT_ID, clientSecret: env.GITHUB_SECRET }
  },
  plugins: [twoFactor(), organization(), apiKey()]
});
```

**Use Better Auth when**: Self-hosted requirement, full customization needed, Drizzle/Prisma ORM integration, no vendor dependency.

### Auth.js v5 (NextAuth)
Rebranded and overhauled for App Router. Still widely used in the Next.js ecosystem but Better Auth has overtaken it for new projects.

---

## 6. File Storage

### Cloudflare R2 (Zero Egress Cost)
**Critical warning**: Never expose `*.r2.dev` development URLs in production. R2 public buckets via `*.r2.dev` URLs have per-request limits and no SLA. Always configure a custom domain with a Cloudflare Worker proxy for production.

```typescript
// Correct: Worker-proxied R2 access
const object = await env.R2_BUCKET.get(key);
if (!object) return new Response("Not Found", { status: 404 });
return new Response(object.body, {
  headers: { "Content-Type": object.httpMetadata?.contentType || "application/octet-stream" }
});
```

**Why R2**: Zero egress fees (vs. AWS S3 which charges $0.09/GB egress). For high-read workloads (images, videos, large files), egress costs on S3 can dwarf storage costs. R2 + Cloudflare CDN eliminates this entirely.

### Vercel Blob
Simplest integration for Vercel-deployed apps. Edge-optimized, automatic CDN. $0.023/GB storage, $0.10/GB bandwidth. Best for Next.js + Vercel deployments where simplicity trumps cost optimization.

### AWS S3
Industry standard. Use when: multi-cloud strategy, existing AWS infrastructure, need advanced features (intelligent tiering, lifecycle policies, replication, S3 Select).

---

## 7. Deployment and Hosting

### Decision Matrix by Scale

| Stage | Platform | Monthly Cost (Typical) |
|---|---|---|
| Prototype / early | **Vercel** (hobby/pro) | $0–$20 |
| Small production | **Railway** | $20–$100 |
| Medium production | **Fly.io** | $50–$500 |
| Scale-up | **Render** or **Railway** enterprise | $100–$2,000 |
| Enterprise | **AWS/GCP/Azure** or **Vercel Enterprise** | $500–$20,000+ |

### Vercel (Frontend + Serverless)
Optimal for Next.js, Astro, SvelteKit. Zero-config deployments, global edge network, preview deployments per-PR. Functions run as serverless (Node.js, Edge Runtime). **15-second timeout on Hobby** (300s on Pro).

### Railway (Full-Stack Simplicity)
Best balance of simplicity and power for full-stack apps. Run any Docker container, PostgreSQL, Redis, MySQL. Automatic deploys from GitHub. Pricing: CPU + RAM usage-based.

### Fly.io (Global Container Deployment)
Deploy Docker containers globally. Persistent volumes, private networking, Postgres via Fly Postgres. Best for: stateful apps, WebSocket-heavy (agent systems), when latency to specific regions matters.

### AWS (When You Need It)
Use AWS when: compliance requirements (HIPAA, FedRAMP, SOC 2), existing enterprise contracts, >$10K/month infrastructure (negotiated pricing), multi-region active-active, or SLA requirements beyond managed platforms.

**AWS stack for SaaS (2026)**: ECS Fargate + ALB + RDS Aurora Serverless v2 + S3 + CloudFront + Route 53 + Certificate Manager + Secrets Manager.

---

## 8. TypeScript Monorepo Architecture

### Turborepo (Standard for Monorepos)
Task orchestration for monorepos. Caches build artifacts across CI runs — teams report 80-90% CI time reduction after adding remote caching.

```json
// turbo.json
{
  "pipeline": {
    "build": { "dependsOn": ["^build"], "outputs": [".next/**", "dist/**"] },
    "test": { "dependsOn": ["build"], "outputs": ["coverage/**"] },
    "lint": {}
  }
}
```

**Workspace structure**:
```
apps/
  web/          # Next.js app
  api/          # Hono/Express API
packages/
  ui/           # Shared React components
  db/           # Drizzle schema + migrations
  auth/         # Shared auth utilities
  validators/   # Shared Zod schemas
```

### Zod (Validation Everywhere)
Runtime validation that serves as the source of truth for TypeScript types. Use Zod at every data boundary: API inputs, form validation, environment variables, database results.

```typescript
const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  ANTHROPIC_API_KEY: z.string().min(1),
  NODE_ENV: z.enum(["development", "production", "test"])
});

export const env = envSchema.parse(process.env); // Throws at startup if misconfigured
```

---

## 9. State Management

### TanStack Query (Server State — Default)
Replaces manual fetch + useState for server state management. Handles caching, background refetching, optimistic updates, and loading/error states.

```typescript
const { data: user, isLoading } = useQuery({
  queryKey: ["user", userId],
  queryFn: () => fetchUser(userId),
  staleTime: 5 * 60 * 1000 // 5 minutes
});

const mutation = useMutation({
  mutationFn: updateUser,
  onSuccess: () => queryClient.invalidateQueries({ queryKey: ["user"] })
});
```

### Zustand (Client State — 50% Adoption)
Minimal, unopinionated global state. Better than Redux for 90% of use cases — no boilerplate, no context provider hell.

```typescript
const useStore = create<AppStore>((set) => ({
  sidebarOpen: false,
  toggleSidebar: () => set((state) => ({ sidebarOpen: !state.sidebarOpen })),
  theme: "system",
  setTheme: (theme) => set({ theme })
}));
```

### Jotai (Atomic State)
Atom-based state inspired by Recoil. Better than Zustand when state has complex derived/computed relationships. Each atom is a small, independent piece of state.

### React Hook Form + Zod (Forms)
Standard pairing: React Hook Form for form state + Zod for validation schema.

```typescript
const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8)
});

const { register, handleSubmit, formState: { errors } } = useForm({
  resolver: zodResolver(schema)
});
```

---

## 10. Testing Strategy

### Vitest (Unit + Integration)
Vite-native test runner replacing Jest. 2-10× faster due to native ESM support and Vite transform reuse.

**2026 feature**: Vitest browser mode — runs tests in real browsers (Chromium, Firefox, WebKit) with the same API as Node tests. Replaces JSDOM for browser-behavior-dependent tests.

```typescript
import { describe, it, expect, vi } from "vitest";

describe("calculateDiscount", () => {
  it("applies 10% to orders over $100", () => {
    expect(calculateDiscount(150)).toBe(135);
  });
  it("no discount under $100", () => {
    expect(calculateDiscount(80)).toBe(80);
  });
});
```

### MSW (API Mocking)
Mock Service Worker intercepts HTTP requests at the network level — works identically in browser (via Service Worker) and Node.js (via interceptors). No more `jest.mock()` fetch hacks.

```typescript
const handlers = [
  http.get("/api/users/:id", ({ params }) => {
    return HttpResponse.json({ id: params.id, name: "Alice" });
  })
];
```

### Playwright (E2E — Replacing Cypress)
Playwright overtook Cypress as the dominant E2E framework by 2025. Supports Chromium, Firefox, and WebKit; runs cross-browser in CI; built-in test trace viewer for debugging failures.

```typescript
test("user can complete checkout", async ({ page }) => {
  await page.goto("/shop");
  await page.click("[data-testid='add-to-cart']");
  await page.click("[data-testid='checkout']");
  await expect(page.locator("[data-testid='order-confirmation']")).toBeVisible();
});
```

**Testing pyramid for SaaS (2026)**:
- Unit (70%): Vitest for pure functions, utilities, validation
- Integration (20%): Vitest + MSW for API routes, database queries
- E2E (10%): Playwright for critical user flows (sign-up, checkout, core feature)

---

## 11. Performance: Core Web Vitals

### 2026 Targets (Google PageSpeed)

| Metric | Good | Needs Improvement | Poor |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | ≤2.5s | 2.5–4.0s | >4.0s |
| **INP** (Interaction to Next Paint) | ≤200ms | 200–500ms | >500ms |
| **CLS** (Cumulative Layout Shift) | ≤0.1 | 0.1–0.25 | >0.25 |

**INP replaced FID** in March 2024 as the interactivity metric. INP measures responsiveness of all interactions, not just the first. Most sites fail INP due to long JavaScript tasks (>50ms).

### Next.js Performance Patterns

**Partial Prerendering (PPR)** — the critical 2026 optimization:
```typescript
// app/page.tsx — static shell renders instantly
export default function Page() {
  return (
    <main>
      <StaticHeader />  {/* Renders at build time */}
      <Suspense fallback={<DashboardSkeleton />}>
        <DynamicDashboard />  {/* Streams after initial response */}
      </Suspense>
    </main>
  );
}
```

**Image optimization**: Always use `next/image`. Automatic WebP/AVIF conversion, lazy loading, aspect ratio preservation, blur placeholder.

**Font optimization**: `next/font` with `display: swap` and preloading. Self-hosts Google Fonts to eliminate external DNS lookup latency.

**Bundle analysis**: `@next/bundle-analyzer` + `bundlewatch` in CI. Target <200KB first load JS for content sites; <500KB for complex apps.

---

## 12. Security: OWASP Top 10:2025

### Ranking Changes (2025 update)
1. **Broken Access Control** (was #1 in 2021) — still #1
2. **Cryptographic Failures**
3. **Injection** (SQL, NoSQL, OS, LDAP)
4. **Insecure Design**
5. **Security Misconfiguration**
6. **Vulnerable and Outdated Components**
7. **Identification and Authentication Failures**
8. **Software and Data Integrity Failures** ← **NEW: Supply chain attacks moved up**
9. **Security Logging and Monitoring Failures**
10. **Server-Side Request Forgery (SSRF)**

**Supply chain attacks** (A08) are the fastest-growing threat in 2025-2026. NPM ecosystem attacks (malicious packages, dependency confusion, typosquatting) affected major platforms.

### Critical Protections for Full-Stack Apps

**Content Security Policy (CSP)**:
```typescript
// Next.js middleware
const cspHeader = `
  default-src 'self';
  script-src 'self' 'nonce-${nonce}';
  style-src 'self' 'nonce-${nonce}';
  img-src 'self' blob: data:;
  connect-src 'self' https://api.anthropic.com;
  frame-ancestors 'none';
`;
```

**SQL Injection**: Use parameterized queries exclusively. Drizzle ORM's query builder is parameterized by default. Never interpolate user input into SQL strings.

**CORS**: Allow only specific, known origins in production. Never `Access-Control-Allow-Origin: *` for authenticated endpoints.

**Rate limiting**: Implement at the edge (Cloudflare Rate Limiting, Vercel Middleware, Upstash Ratelimit) — not just at the API layer.

**Dependency scanning**: `npm audit` in CI pipeline. Use Snyk or Socket Security for continuous dependency vulnerability monitoring. Lock files (`package-lock.json`, `pnpm-lock.yaml`) are security artifacts — commit them.

---

## 13. Mobile and Cross-Platform

### React Native + Expo (Standard Mobile Stack)
**Expo SDK 52+ (2025)**: New Architecture (Fabric renderer + JSI) enabled by default. Significantly better performance, synchronous native module calls, and Hermes engine optimizations.

**Expo Router (file-based)**: Same routing paradigm as Next.js App Router. Universal links, shared navigation state between web and native.

```typescript
// app/(tabs)/index.tsx — Expo Router
export default function HomeScreen() {
  return <View><Text>Home</Text></View>;
}
```

**Expo EAS (Build Service)**: Cloud builds for iOS and Android without local Xcode/Android Studio. OTA updates via `expo-updates` for JS-only changes (bypasses app store review for minor updates).

### Tauri 2.x (Desktop → Mobile, 2024+)
Tauri 2.0 added iOS and Android support — making it a cross-platform framework for desktop + mobile from a single Rust + WebView codebase.

**Tauri vs Electron**:
- **Bundle size**: Tauri ~600KB vs Electron ~150MB (96% smaller)
- **Memory**: Tauri uses system WebView; Electron bundles Chromium (~300MB memory overhead)
- **Performance**: Tauri Rust core significantly faster for CPU-bound work
- **Ecosystem**: Electron has larger plugin ecosystem; Tauri catching up rapidly

**Use Tauri when**: Desktop app where bundle size matters, system-level access (filesystem, hardware), privacy-sensitive (no Electron renderer process isolation concerns), or cross-platform desktop + mobile from one codebase.

---

## 14. AI-Assisted Development Workflow (2026)

### Cursor + Claude Code Integration
The dominant 2026 workflow: **Cursor for daily editing + Claude Code for complex multi-file tasks**.

**Cursor** (18% adoption, $2B ARR):
- In-file autocomplete and Tab completion (Copilot-class)
- Composer: multi-file edits with context from the full codebase
- Best for: incremental development, quick functions, refactoring within known files

**Claude Code** (18% adoption, 6× growth in 9 months):
- Terminal-native; reads full codebase context
- Best for: architectural changes, cross-repository work, complex debugging, test generation
- Hooks system for custom pre/post-tool-use workflows

**Workflow**: Write feature spec → Claude Code for architecture + scaffolding → Cursor for iterative development → Claude Code for test generation and review.

### AI-Assisted Development Principles
- AI tools amplify existing practices — strong engineers benefit more than weak ones
- Code review discipline becomes more important, not less, with AI generation
- Test generation is the highest-ROI AI coding use case (eliminates most tedious coverage work)
- Prompt the AI with context: file structure, existing patterns, constraints — not just the feature request

---

## Full-Stack Stack Decision Template (2026)

| Layer | Default Choice | Alternative |
|---|---|---|
| **Frontend** | Next.js 15+ | SvelteKit (performance), Astro (content) |
| **API** | tRPC (monorepo) / Hono (standalone) | REST+OpenAPI, GraphQL |
| **Database** | Supabase + Drizzle | Neon (branching), PlanetScale (MySQL) |
| **Auth** | Better Auth | Clerk (managed), Auth.js v5 |
| **File Storage** | Cloudflare R2 | Vercel Blob (simplicity), S3 (enterprise) |
| **State (server)** | TanStack Query | SWR |
| **State (client)** | Zustand | Jotai (atomic), Redux Toolkit (large apps) |
| **Forms** | React Hook Form + Zod | TanStack Form |
| **Styling** | Tailwind CSS v4 | CSS Modules |
| **UI Components** | shadcn/ui + Radix | Headless UI |
| **Testing** | Vitest + Playwright + MSW | Jest (legacy) |
| **Monorepo** | Turborepo | Nx |
| **Deployment** | Vercel | Railway, Fly.io, AWS |
| **AI Integration** | Vercel AI SDK | LangChain.js |
| **Mobile** | React Native + Expo | Flutter |
| **Desktop** | Tauri 2.x | Electron |
