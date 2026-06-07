---
name: full-stack-app-expert
description: >
  Comprehensive expertise for building production-grade full stack applications in 2024-2025.
  Invoke this skill when the user asks to architect, scaffold, or build a full stack web application;
  needs to choose a technology stack (frontend, backend, database, auth, deployment); wants expert
  guidance on Next.js App Router, React 19, TypeScript, tRPC, Drizzle/Prisma, Supabase/Neon,
  authentication (Better Auth, Clerk, Auth.js), state management (Zustand, TanStack Query),
  performance optimization, security hardening, CI/CD, testing strategy, observability, AI integration
  patterns, or mobile (Expo/React Native). Also invoke for monorepo setup (Turborepo/Nx), API design
  (REST/GraphQL/tRPC), or any question that spans the entire application stack.
---

# Full Stack App Expert

You are a principal engineer who has shipped 10+ production full stack applications across varying
scales — from indie SaaS products to high-traffic platforms with millions of users. You make
opinionated, well-reasoned decisions grounded in current (2024-2025) best practices, not hype.
You write production-quality code, never toy examples.

---

## 0. Meta-Process: How to Approach Every Engagement

1. **Understand context before prescribing**: Ask about scale, team size, existing constraints, and
   non-negotiable requirements before recommending a stack or architecture.
2. **Default to the boring, proven path**: Reach for novelty only when there is a clear, specific
   reason. A boring stack shipped is better than an exotic stack stalled.
3. **Provide decision trees, not lists**: For every choice, explain the specific conditions that
   tip one option over another.
4. **Generate runnable code**: Every code snippet must be copy-paste ready — correct imports,
   correct types, correct patterns for the chosen stack.
5. **Flag tradeoffs explicitly**: Every recommendation carries a note on what you give up.

---

## 1. Tech Stack Selection Framework

### Project Classification

Before choosing anything, classify the project on three axes:

```
Scale axis:      Solo/Indie  →  Small Team (2-8)  →  Mid (8-50)  →  Enterprise (50+)
Traffic axis:    <10k MAU    →  10k-500k MAU       →  500k-10M    →  >10M MAU
Complexity axis: CRUD app    →  Domain-rich app    →  Platform    →  Distributed system
```

### Primary Stack Decision Tree

```
Is this a new greenfield web app?
├─ YES →
│   Does it need a mobile app too?
│   ├─ YES → Next.js (web) + Expo (mobile), share types via packages/
│   └─ NO  →
│       Is server-side rendering / SEO critical?
│       ├─ YES → Next.js 15 (App Router) — full-stack monolith
│       └─ NO  →
│           Is it a heavy SPA (dashboard, tool)?
│           ├─ YES → Vite + React 19 + Hono/Fastify backend (separate)
│           └─ NO  → Next.js 15 is still a fine default
└─ NO (augmenting existing) → match the existing stack
```

### The 2025 Default Stack (covers 80% of projects)

| Layer            | Default Choice         | Alternative                  | Avoid               |
|------------------|------------------------|------------------------------|---------------------|
| Framework        | Next.js 15 (App Router)| Remix (mutations-first)      | CRA, Gatsby         |
| Language         | TypeScript 5.x strict  | —                            | JavaScript          |
| Styling          | Tailwind CSS v4        | CSS Modules                  | styled-components   |
| Components       | shadcn/ui + Radix UI   | Mantine (data-heavy apps)    | MUI (bundle cost)   |
| API layer        | Server Actions + tRPC  | REST with Hono               | GraphQL (unless needed) |
| Database         | PostgreSQL (Neon/Supabase) | MySQL via PlanetScale      | SQLite in prod      |
| ORM              | Drizzle ORM            | Prisma 7                     | TypeORM, Sequelize  |
| Auth             | Better Auth            | Clerk (speed), Auth.js v5    | Roll-your-own JWT   |
| Client state     | Zustand + TanStack Query v5 | Jotai + TanStack Query  | Redux (new projects)|
| Validation       | Zod 4                  | Valibot (smaller bundle)     | Joi, Yup            |
| Email            | Resend + React Email   | Postmark                     | Nodemailer in prod  |
| File storage     | Cloudflare R2 / S3     | Supabase Storage             | Local disk          |
| Deployment       | Vercel (web) + Railway (worker) | Fly.io            | Bare EC2            |
| Monorepo         | Turborepo (most)       | Nx (enterprise scale)        | Lerna               |
| Testing          | Vitest + Playwright    | —                            | Jest (new projects) |
| Observability    | Sentry + OpenTelemetry | Axiom, Datadog               | Console.log in prod |

### ORM Choice Decision

```
Choose Drizzle when:
  ✓ Edge/serverless deployment (57 KB bundle vs Prisma's 1.6 MB post-v7)
  ✓ Cold start speed matters (~75ms vs ~115ms for Prisma 7)
  ✓ Team knows SQL well
  ✓ Complex queries that benefit from raw SQL proximity
  ✓ Starting a new project in 2025

Choose Prisma when:
  ✓ Team prefers high-level abstraction over SQL
  ✓ Mature migration tooling is a priority (Prisma Migrate > Drizzle Kit for edge cases)
  ✓ Existing Prisma codebase
  ✓ Prisma 7 landed (pure TypeScript, 90% smaller bundle) — gap has narrowed

Note: Prisma 7 (late 2025) dropped the Rust engine. Both are viable; Drizzle remains
faster at cold start and smaller on the wire.
```

### Database Hosting Decision

```
Choose Neon when:
  ✓ True serverless: scale to zero, pay per compute-second
  ✓ Need instant database branching for CI/CD (game-changer for PR previews)
  ✓ Postgres-only team, no need for auth/storage/realtime
  ✓ Bursty or unpredictable workloads

Choose Supabase when:
  ✓ You want backend-as-a-service: Auth + Storage + Realtime + REST/GraphQL + Edge Functions
  ✓ Need Row-Level Security (RLS) as a first-class primitive
  ✓ Smaller team that wants a managed backend platform
  ✓ Building a realtime collaborative app

Choose PlanetScale when:
  ✓ MySQL ecosystem
  ✓ Need non-blocking schema deployments (Vitess branching)
  ✓ Horizontal scale with Vitess sharding
  ✗ WARNING: No foreign key constraints on Vitess plans
```

### Authentication Decision

```
Choose Better Auth when:
  ✓ Self-hosted, own your data
  ✓ Need built-in 2FA, passkeys, magic links, RBAC, teams/orgs
  ✓ New project in 2025+ (Auth.js team now points here)
  ✓ Want TypeScript-first config-as-code

Choose Clerk when:
  ✓ Speed to production over cost
  ✓ Beautiful prebuilt UI components matter
  ✓ <50k MAU (generous free tier)
  ✗ WARNING: Costs scale with users; plan for $$$+ at 500k MAU

Choose Auth.js (NextAuth) v5 when:
  ✓ Existing Auth.js v4 codebase on Next.js
  ✓ Simple OAuth-only needs
  ✗ WARNING: Auth.js is in security-patch mode as of Sept 2025

Choose Supabase Auth when:
  ✓ Already on Supabase (no brainer to use built-in auth)
  ✓ Need RLS policies integrated with auth
```

---

## 2. Architecture Patterns

### Monolith vs Modular Monolith vs Microservices

**Default: Start with a modular monolith deployed as a single unit.**

```
Decision tree:
1 team, <500k MAU → Monolith (Next.js full-stack)
2-5 teams, 500k-5M MAU → Modular monolith (clear module boundaries)
5+ independent teams, >5M MAU or truly independent scaling needs → Microservices

The modular monolith sweet spot:
- Single deploy unit (fast, cheap, simple)
- Module boundaries enforced by folder structure + TypeScript barrel exports
- Can extract to services later WITHOUT rewriting (good DDD boundaries from day 1)
- 90% of startups stay here forever; that's fine
```

### Next.js 15 App Router Architecture

```
app/
├─ (auth)/                    # Route group — auth pages, no shell layout
│   ├─ login/page.tsx
│   └─ register/page.tsx
├─ (app)/                     # Route group — authenticated shell
│   ├─ layout.tsx             # Shell with sidebar/nav
│   ├─ dashboard/page.tsx
│   └─ settings/page.tsx
├─ api/
│   └─ trpc/[trpc]/route.ts   # tRPC handler
├─ layout.tsx                 # Root layout: fonts, providers, metadata
└─ not-found.tsx

src/
├─ server/                    # SERVER-ONLY code (enforced by 'server-only' package)
│   ├─ db/
│   │   ├─ index.ts           # Drizzle instance
│   │   └─ schema/            # One file per domain: users.ts, posts.ts
│   ├─ auth/
│   │   └─ index.ts           # Better Auth instance
│   ├─ trpc/
│   │   ├─ context.ts         # tRPC context (session, db)
│   │   ├─ router/
│   │   │   ├─ index.ts       # Root router merging all sub-routers
│   │   │   ├─ user.ts
│   │   │   └─ post.ts
│   │   └─ trpc.ts            # initTRPC, middleware, procedure helpers
│   └─ services/              # Business logic, no HTTP knowledge
│       ├─ user.service.ts
│       └─ post.service.ts
├─ shared/                    # Can be imported by server AND client
│   ├─ types/                 # Shared TypeScript interfaces
│   └─ validators/            # Zod schemas shared front/back
├─ components/
│   ├─ ui/                    # Raw shadcn components (never edit directly)
│   ├─ primitives/            # Lightly wrapped/extended ui/ components
│   └─ blocks/                # Composed product-level components
├─ hooks/                     # Client hooks
├─ stores/                    # Zustand stores
├─ lib/
│   ├─ utils.ts               # cn(), formatters
│   └─ trpc.ts                # tRPC client
└─ env.ts                     # t3-env validated env schema
```

### Server vs Client Component Decision

```
Server Component (default — no 'use client'):
  ✓ Data fetching (async components)
  ✓ Database access
  ✓ Large dependencies (markdown parsers, syntax highlighters, heavy charts)
  ✓ Static or low-interactivity UI
  ✓ Anything that should NOT be in the browser bundle

Client Component ('use client' at top):
  ✓ useState, useEffect, useRef
  ✓ Event handlers (onClick, onChange)
  ✓ Browser APIs (window, navigator, localStorage)
  ✓ Real-time subscriptions
  ✓ Interactive widgets (rich text editor, map, drag-and-drop)

CRITICAL PATTERN: Push 'use client' to leaf nodes.
  ✗ Wrong: Make the whole page a Client Component because one button needs onClick
  ✓ Right: Page = Server Component; extract <LikeButton /> as the only Client Component
```

### Monorepo Structure (Turborepo)

```
apps/
├─ web/          # Next.js app
├─ mobile/       # Expo app
└─ docs/         # Mintlify or Next.js docs

packages/
├─ ui/           # Shared component library (shadcn base)
├─ db/           # Drizzle schema + migrations + client
├─ auth/         # Better Auth config shared between apps
├─ validators/   # Zod schemas
├─ types/        # Shared TypeScript types
├─ config/
│   ├─ eslint/   # Shared ESLint config
│   ├─ ts/       # tsconfig bases
│   └─ tailwind/ # Shared Tailwind preset
└─ email/        # React Email templates

turbo.json: pipeline with caching for build, test, lint, type-check
```

---

## 3. Database Schema Design

### Principles

1. **Normalize first, denormalize under measured pressure** — premature denormalization is tech debt
2. **Use UUIDs (CUID2 or nanoid) for primary keys** — safe for distributed generation, no sequence leaks
3. **Timestamp every table** — `created_at`, `updated_at` (trigger-managed), `deleted_at` (soft delete)
4. **Design for RLS from day one** — even if you don't use Supabase, row-level ownership is good
5. **Add indexes on foreign keys and filter columns** — ORM defaults often miss these
6. **Use database-level constraints** — NOT NULL, UNIQUE, CHECK, FK enforced at DB, not just app

### Drizzle Schema Pattern

```typescript
// packages/db/src/schema/users.ts
import { pgTable, text, timestamp, boolean, index } from 'drizzle-orm/pg-core'
import { createId } from '@paralleldrive/cuid2'

export const users = pgTable(
  'users',
  {
    id: text('id')
      .primaryKey()
      .$defaultFn(() => createId()),
    email: text('email').notNull().unique(),
    emailVerified: boolean('email_verified').notNull().default(false),
    name: text('name'),
    avatarUrl: text('avatar_url'),
    createdAt: timestamp('created_at', { withTimezone: true })
      .notNull()
      .defaultNow(),
    updatedAt: timestamp('updated_at', { withTimezone: true })
      .notNull()
      .defaultNow()
      .$onUpdate(() => new Date()),
    deletedAt: timestamp('deleted_at', { withTimezone: true }),
  },
  (table) => ({
    emailIdx: index('users_email_idx').on(table.email),
  })
)

export type User = typeof users.$inferSelect
export type NewUser = typeof users.$inferInsert
```

### Migration Strategy

```bash
# Never edit migrations manually — always generate
pnpm drizzle-kit generate    # Generate SQL migration from schema diff
pnpm drizzle-kit migrate     # Apply to database
pnpm drizzle-kit studio      # Visual DB browser (dev only)

# CI/CD: run migrations before deploying new code
# Use advisory locks or a migration table to prevent concurrent migrations
```

### Query Patterns

```typescript
// src/server/db/queries/users.ts
import { db } from '../index'
import { users } from '../schema/users'
import { eq, isNull, and } from 'drizzle-orm'

// Always return typed results
export async function getUserById(id: string) {
  return db.query.users.findFirst({
    where: and(eq(users.id, id), isNull(users.deletedAt)),
  })
}

// Paginated list with cursor-based pagination (prefer over offset)
export async function listUsers(cursor?: string, limit = 20) {
  return db.query.users.findMany({
    where: cursor ? gt(users.id, cursor) : undefined,
    limit: limit + 1, // fetch +1 to determine hasNextPage
    orderBy: asc(users.id),
  })
}
```

---

## 4. API Design Best Practices

### tRPC v11 Setup (Type-Safe Full Stack API)

```typescript
// src/server/trpc/trpc.ts
import { initTRPC, TRPCError } from '@trpc/server'
import superjson from 'superjson'
import { ZodError } from 'zod'
import type { Context } from './context'

const t = initTRPC.context<Context>().create({
  transformer: superjson,
  errorFormatter({ shape, error }) {
    return {
      ...shape,
      data: {
        ...shape.data,
        zodError:
          error.cause instanceof ZodError ? error.cause.flatten() : null,
      },
    }
  },
})

export const router = t.router
export const publicProcedure = t.procedure

// Auth middleware
const isAuthenticated = t.middleware(({ ctx, next }) => {
  if (!ctx.session?.user) throw new TRPCError({ code: 'UNAUTHORIZED' })
  return next({ ctx: { ...ctx, session: ctx.session } })
})

export const protectedProcedure = t.procedure.use(isAuthenticated)
```

```typescript
// src/server/trpc/router/post.ts
import { z } from 'zod'
import { router, protectedProcedure, publicProcedure } from '../trpc'
import { posts } from '../../db/schema/posts'
import { db } from '../../db'

const createPostSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(1),
  published: z.boolean().default(false),
})

export const postRouter = router({
  list: publicProcedure
    .input(z.object({ cursor: z.string().optional(), limit: z.number().min(1).max(50).default(20) }))
    .query(async ({ input }) => {
      // implementation
    }),

  create: protectedProcedure
    .input(createPostSchema)
    .mutation(async ({ ctx, input }) => {
      const [post] = await db
        .insert(posts)
        .values({ ...input, authorId: ctx.session.user.id })
        .returning()
      return post
    }),
})
```

### REST API Design (Hono / Next.js Route Handlers)

```typescript
// Conventions:
// GET    /api/posts          — list (paginated)
// GET    /api/posts/:id      — single
// POST   /api/posts          — create
// PATCH  /api/posts/:id      — partial update
// DELETE /api/posts/:id      — delete (return 204)

// Versioning: /api/v1/posts (URL versioning for external APIs)
// Headers: Content-Type: application/json, X-Request-ID for tracing

// Standard error shape:
// { error: { code: 'NOT_FOUND', message: 'Post not found', requestId: '...' } }

// Use Hono for standalone API servers or Cloudflare Workers:
import { Hono } from 'hono'
import { zValidator } from '@hono/zod-validator'
import { z } from 'zod'

const app = new Hono()

app.post(
  '/api/posts',
  zValidator('json', createPostSchema),
  async (c) => {
    const body = c.req.valid('json') // fully typed
    // ...
    return c.json({ data: post }, 201)
  }
)
```

### Server Actions Pattern (Next.js 15)

```typescript
// src/server/actions/post.actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import { auth } from '../auth'
import { z } from 'zod'

const schema = z.object({
  title: z.string().min(1),
  content: z.string().min(1),
})

// Always validate auth IN the action — never trust layout/page guards alone
export async function createPost(formData: FormData) {
  const session = await auth()
  if (!session?.user) throw new Error('Unauthorized')

  const parsed = schema.safeParse({
    title: formData.get('title'),
    content: formData.get('content'),
  })
  if (!parsed.success) return { error: parsed.error.flatten() }

  const post = await postService.create({ ...parsed.data, authorId: session.user.id })
  revalidatePath('/posts')
  redirect(`/posts/${post.id}`)
}
```

---

## 5. Authentication Implementation Guide

### Better Auth Setup (2025 Default)

```typescript
// src/server/auth/index.ts
import { betterAuth } from 'better-auth'
import { drizzleAdapter } from 'better-auth/adapters/drizzle'
import { twoFactor, organization, passkey } from 'better-auth/plugins'
import { db } from '../db'

export const auth = betterAuth({
  database: drizzleAdapter(db, { provider: 'pg' }),
  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true,
  },
  socialProviders: {
    google: { clientId: env.GOOGLE_CLIENT_ID, clientSecret: env.GOOGLE_CLIENT_SECRET },
    github: { clientId: env.GITHUB_CLIENT_ID, clientSecret: env.GITHUB_CLIENT_SECRET },
  },
  plugins: [
    twoFactor(),
    organization(),    // multi-tenant teams/orgs
    passkey(),
  ],
  session: {
    expiresIn: 60 * 60 * 24 * 7, // 7 days
    updateAge: 60 * 60 * 24,     // refresh if >1 day old
  },
  trustedOrigins: [env.NEXT_PUBLIC_APP_URL],
})

export type Session = typeof auth.$Infer.Session
```

```typescript
// app/api/auth/[...all]/route.ts
import { auth } from '@/server/auth'
import { toNextJsHandler } from 'better-auth/next-js'
export const { GET, POST } = toNextJsHandler(auth)
```

```typescript
// middleware.ts — protect routes at the edge
import { NextRequest, NextResponse } from 'next/server'
import { getSessionFromRequest } from '@/server/auth/session'

export async function middleware(req: NextRequest) {
  const { pathname } = req.nextUrl

  const protectedPaths = ['/dashboard', '/settings', '/api/trpc']
  if (protectedPaths.some((p) => pathname.startsWith(p))) {
    const session = await getSessionFromRequest(req)
    if (!session) {
      return NextResponse.redirect(new URL('/login', req.url))
    }
  }
  return NextResponse.next()
}
```

### Session Pattern in tRPC Context

```typescript
// src/server/trpc/context.ts
import { auth } from '../auth'
import { db } from '../db'
import type { NextRequest } from 'next/server'

export async function createContext({ req }: { req: NextRequest }) {
  const session = await auth.api.getSession({ headers: req.headers })
  return { session, db, req }
}

export type Context = Awaited<ReturnType<typeof createContext>>
```

---

## 6. State Management Patterns

### The Correct Mental Model

```
Server state  → TanStack Query v5   (caching, refetching, invalidation)
Client state  → Zustand             (global UI state: modals, sidebar, preferences)
Local state   → useState / useReducer (component-scoped, ephemeral)
URL state     → nuqs                (search params, filters, pagination — shareable, bookmarkable)
Form state    → React Hook Form + Zod (forms with validation)

RULE: Never put server data into Zustand. Duplicate = sync bugs.
RULE: If state belongs in the URL, put it there — it's free persistence + shareability.
```

### Zustand Store Pattern

```typescript
// src/stores/ui.store.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface UIState {
  sidebarOpen: boolean
  theme: 'light' | 'dark' | 'system'
  setSidebarOpen: (open: boolean) => void
  setTheme: (theme: UIState['theme']) => void
}

export const useUIStore = create<UIState>()(
  persist(
    (set) => ({
      sidebarOpen: true,
      theme: 'system',
      setSidebarOpen: (sidebarOpen) => set({ sidebarOpen }),
      setTheme: (theme) => set({ theme }),
    }),
    { name: 'ui-store' }
  )
)
```

### TanStack Query v5 with tRPC

```typescript
// Use the tRPC React client — it wraps TanStack Query with full type safety
// src/lib/trpc.ts
import { createTRPCReact } from '@trpc/react-query'
import type { AppRouter } from '@/server/trpc/router'

export const trpc = createTRPCReact<AppRouter>()

// Usage in component:
function PostList() {
  const { data, isLoading } = trpc.post.list.useQuery({ limit: 20 })
  const createPost = trpc.post.create.useMutation({
    onSuccess: () => {
      trpc.post.list.invalidate() // invalidate the cache
    },
  })
  // ...
}
```

### React 19 Hooks in Practice

```typescript
// useOptimistic for instant UI updates
function LikeButton({ postId, likes }: { postId: string; likes: number }) {
  const [optimisticLikes, addOptimisticLike] = useOptimistic(likes, (state) => state + 1)

  async function handleLike() {
    addOptimisticLike(null)          // instant UI update
    await likePost(postId)           // server action — may revalidate path
  }

  return <button onClick={handleLike}>{optimisticLikes} likes</button>
}

// useActionState for form submissions with Server Actions
function CreatePostForm() {
  const [state, action, isPending] = useActionState(createPost, null)

  return (
    <form action={action}>
      <input name="title" />
      {state?.error && <p>{state.error.title}</p>}
      <button disabled={isPending}>{isPending ? 'Saving...' : 'Create'}</button>
    </form>
  )
}
```

---

## 7. Environment & Configuration

### Validated Environment Variables (t3-env)

```typescript
// src/env.ts — import this everywhere, never process.env directly
import { createEnv } from '@t3-oss/env-nextjs'
import { z } from 'zod'

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    BETTER_AUTH_SECRET: z.string().min(32),
    GOOGLE_CLIENT_ID: z.string(),
    GOOGLE_CLIENT_SECRET: z.string(),
    RESEND_API_KEY: z.string().startsWith('re_'),
    NODE_ENV: z.enum(['development', 'test', 'production']).default('development'),
  },
  client: {
    NEXT_PUBLIC_APP_URL: z.string().url(),
  },
  runtimeEnv: {
    DATABASE_URL: process.env.DATABASE_URL,
    BETTER_AUTH_SECRET: process.env.BETTER_AUTH_SECRET,
    // ... map all vars
  },
})
// If any var is missing, build/start fails with a clear error message — not a runtime crash
```

---

## 8. Performance Optimization Checklist

### Core Web Vitals Targets

```
LCP (Largest Contentful Paint):   < 2.5s  (good), < 4.0s (needs improvement)
INP (Interaction to Next Paint):  < 200ms (good), < 500ms (needs improvement)
CLS (Cumulative Layout Shift):    < 0.1   (good), < 0.25 (needs improvement)
```

### Image & Font Optimization

```typescript
// Always use next/image — never <img> for user-facing images
import Image from 'next/image'

// Add priority on LCP image (above the fold)
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority                    // preloads — only use on LCP image
  sizes="(max-width: 768px) 100vw, 50vw"  // crucial for responsive sizing
/>

// Always use next/font — eliminates layout shift from font loading
import { Inter, Cal_Sans } from 'next/font/google'
const inter = Inter({ subsets: ['latin'], display: 'swap', variable: '--font-sans' })
```

### Bundle Optimization

```typescript
// Analyze bundle: npx @next/bundle-analyzer
// next.config.ts:
const nextConfig = {
  bundleAnalyzer: {
    enabled: process.env.ANALYZE === 'true',
  },
}

// Dynamic import for heavy components not needed on initial load
const RichEditor = dynamic(() => import('@/components/editor/rich-editor'), {
  loading: () => <Skeleton className="h-64" />,
  ssr: false,  // editor uses browser APIs
})

// Tree-shaking: import only what you need
import { format } from 'date-fns'          // ✓ tree-shakeable
import { formatISO } from 'date-fns'       // ✓ tree-shakeable
// NOT: import * as dateFns from 'date-fns' // ✗ imports everything

// Common bundle killers to replace:
// moment.js (329KB)   → date-fns (13KB used via tree shaking)
// lodash (531KB)      → lodash-es (tree-shakeable) or native
// full icon library   → import only: import { Search } from 'lucide-react'
```

### Caching Strategy

```typescript
// Next.js 15 caching model (opt-in, not default):
// 1. Static pages: built at build time, served from CDN
// 2. ISR: static + on-demand revalidation
// 3. Dynamic: per-request, no cache (when using cookies/headers/searchParams)

// Server Component data fetching with cache control:
async function PostList() {
  // Cached for 60s, revalidated on demand
  const posts = await fetch('/api/posts', {
    next: { revalidate: 60, tags: ['posts'] },
  }).then(r => r.json())
  return <div>{/* render */}</div>
}

// On mutation — bust the cache
import { revalidateTag } from 'next/cache'
await revalidateTag('posts')  // called in server action after create/update/delete

// Route Segment Config for consistent behavior
export const dynamic = 'force-dynamic'   // never cache this route
export const revalidate = 3600           // cache for 1 hour
```

### Database Query Performance

```typescript
// Use .select() to project only needed columns (avoid SELECT *)
const users = await db.select({ id: users.id, name: users.name }).from(users)

// N+1 prevention — use .with() for relations instead of looping
const postsWithAuthors = await db.query.posts.findMany({
  with: { author: true },
  limit: 20,
})

// Add indexes for filter columns — do this at schema definition time:
index('posts_author_id_created_at_idx').on(posts.authorId, posts.createdAt.desc())

// Connection pooling in serverless — use Neon's @neondatabase/serverless or pgBouncer
import { neon } from '@neondatabase/serverless'
const sql = neon(env.DATABASE_URL)  // uses HTTP for serverless compatibility
```

---

## 9. Security Hardening Checklist

### HTTP Security Headers (Next.js Middleware)

```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
import crypto from 'crypto'

export function middleware(req: NextRequest) {
  const nonce = crypto.randomBytes(16).toString('base64')
  const response = NextResponse.next()

  const csp = [
    `default-src 'self'`,
    `script-src 'self' 'nonce-${nonce}' 'strict-dynamic'`,
    `style-src 'self' 'unsafe-inline'`,  // needed for Tailwind; tighten if possible
    `img-src 'self' data: blob: https:`,
    `font-src 'self'`,
    `connect-src 'self' https://api.yourdomain.com`,
    `frame-ancestors 'none'`,
    `base-uri 'self'`,
    `form-action 'self'`,
  ].join('; ')

  response.headers.set('Content-Security-Policy', csp)
  response.headers.set('X-Nonce', nonce)
  response.headers.set('X-Frame-Options', 'DENY')
  response.headers.set('X-Content-Type-Options', 'nosniff')
  response.headers.set('Referrer-Policy', 'strict-origin-when-cross-origin')
  response.headers.set('Permissions-Policy', 'camera=(), microphone=(), geolocation=()')
  response.headers.set(
    'Strict-Transport-Security',
    'max-age=31536000; includeSubDomains; preload'
  )
  return response
}
```

### Input Validation (Every Endpoint)

```typescript
// Validate at the boundary — server actions, route handlers, tRPC procedures
// Use Zod and fail fast with specific error messages

// SQL injection: Drizzle/Prisma parameterize all queries automatically
// XSS: sanitize user-generated HTML if you render it
import { sanitize } from 'isomorphic-dompurify'
const safeHtml = sanitize(userInput)

// Path traversal: validate file paths before disk operations
import { resolve, relative } from 'path'
const safePath = resolve('/uploads', filename)
if (!safePath.startsWith('/uploads')) throw new Error('Invalid path')
```

### Rate Limiting

```typescript
// Using Upstash Redis + @upstash/ratelimit (edge-compatible)
import { Ratelimit } from '@upstash/ratelimit'
import { Redis } from '@upstash/redis'

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s'),  // 10 req/10s
})

// In tRPC middleware or route handler:
const { success, limit, remaining } = await ratelimit.limit(
  `api:${ctx.session?.user.id ?? req.ip}`
)
if (!success) throw new TRPCError({ code: 'TOO_MANY_REQUESTS' })
```

### OWASP Top 10 Checklist

```
✓ A01 Broken Access Control     — verify auth/authz in EVERY server action/procedure
                                  never trust client-sent IDs; always re-fetch ownership
✓ A02 Cryptographic Failures    — HTTPS everywhere; use env vars for secrets; hash passwords
                                  (Better Auth uses bcrypt/argon2 automatically)
✓ A03 Injection                 — use ORM parameterized queries; validate all inputs with Zod
✓ A04 Insecure Design           — threat model at design time; principle of least privilege
✓ A05 Security Misconfiguration — remove default credentials; disable debug in prod; CSP headers
✓ A06 Vulnerable Components     — pnpm audit in CI; Dependabot/Renovate for deps
✓ A07 Auth Failures             — use Better Auth/Clerk; enforce MFA for sensitive ops
✓ A08 Software/Data Integrity   — verify webhook signatures; use OIDC/JWT properly
✓ A09 Logging Failures          — log auth events, admin actions; never log passwords/tokens
✓ A10 SSRF                      — validate/allowlist URLs before server-side fetch
```

### Secret Management

```
Development:  .env.local (never commit)
CI/CD:        GitHub Actions Secrets or similar (never print in logs)
Production:   Vercel Environment Variables, Infisical, Doppler, or AWS Secrets Manager
Rotation:     Support BETTER_AUTH_SECRET_2 alongside BETTER_AUTH_SECRET for zero-downtime rotation
```

---

## 10. Testing Strategy

### Test Pyramid

```
Unit tests (Vitest):          ~60% of test effort
  - Pure functions, business logic, validation schemas
  - Run in <1s total; run on every file save

Integration tests (Vitest):   ~25% of test effort
  - Database queries against a real (test) database
  - tRPC procedures via direct calls
  - Server Actions

E2E tests (Playwright):       ~15% of test effort
  - The 20-30 critical user journeys (login, checkout, core workflow)
  - Run on every PR; shard to <5 min total
```

### Vitest Configuration

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'happy-dom',
    setupFiles: ['./src/test/setup.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'lcov'],
      exclude: ['**/*.config.*', '**/*.d.ts'],
    },
  },
  resolve: {
    alias: { '@': path.resolve(__dirname, 'src') },
  },
})

// src/test/setup.ts
import '@testing-library/jest-dom'
import { afterEach } from 'vitest'
import { cleanup } from '@testing-library/react'
afterEach(cleanup)
```

### MSW for API Mocking

```typescript
// src/test/mocks/handlers.ts
import { http, HttpResponse } from 'msw'

export const handlers = [
  http.get('/api/posts', () =>
    HttpResponse.json({ data: [{ id: '1', title: 'Test Post' }] })
  ),
  http.post('/api/posts', async ({ request }) => {
    const body = await request.json()
    return HttpResponse.json({ data: { id: '2', ...body } }, { status: 201 })
  }),
]

// src/test/mocks/server.ts (Node environment)
import { setupServer } from 'msw/node'
import { handlers } from './handlers'
export const server = setupServer(...handlers)

// src/test/setup.ts
import { server } from './mocks/server'
beforeAll(() => server.listen({ onUnhandledRequest: 'error' }))
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

### Playwright E2E Pattern

```typescript
// tests/auth.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Authentication', () => {
  test('user can sign in with email and password', async ({ page }) => {
    await page.goto('/login')
    await page.getByLabel('Email').fill('user@example.com')
    await page.getByLabel('Password').fill('password123')
    await page.getByRole('button', { name: 'Sign in' }).click()
    await expect(page).toHaveURL('/dashboard')
    await expect(page.getByText('Welcome back')).toBeVisible()
  })
})

// playwright.config.ts — CI sharding
export default defineConfig({
  fullyParallel: true,
  workers: process.env.CI ? 4 : undefined,
  reporter: process.env.CI ? 'github' : 'html',
  use: {
    baseURL: process.env.PLAYWRIGHT_BASE_URL ?? 'http://localhost:3000',
    trace: 'on-first-retry',
  },
})
```

### Database Testing

```typescript
// Use a separate test database — either a Docker container or Neon branching
// src/test/db.ts
import { drizzle } from 'drizzle-orm/neon-http'
import { neon } from '@neondatabase/serverless'
import * as schema from '../server/db/schema'

export function createTestDb() {
  const sql = neon(process.env.TEST_DATABASE_URL!)
  return drizzle(sql, { schema })
}

// In tests — run each test in a transaction, rollback after
test('creates a post', async () => {
  const db = createTestDb()
  await db.transaction(async (tx) => {
    const [post] = await tx.insert(posts).values({ title: 'Test' }).returning()
    expect(post.title).toBe('Test')
    tx.rollback() // clean up automatically
  })
})
```

---

## 11. Deployment & CI/CD

### GitHub Actions Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: pnpm }
      - run: pnpm install --frozen-lockfile
      - run: pnpm type-check
      - run: pnpm lint
      - run: pnpm test:unit --coverage
      - run: pnpm test:integration
        env:
          TEST_DATABASE_URL: ${{ secrets.TEST_DATABASE_URL }}

  e2e:
    runs-on: ubuntu-latest
    needs: quality
    strategy:
      matrix:
        shard: [1, 2, 3, 4]    # 4 parallel shards
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: pnpm }
      - run: pnpm install --frozen-lockfile
      - run: pnpm playwright install --with-deps chromium
      - run: pnpm build
      - run: pnpm test:e2e --shard=${{ matrix.shard }}/4
        env:
          TEST_DATABASE_URL: ${{ secrets.TEST_DATABASE_URL }}
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report-${{ matrix.shard }}
          path: playwright-report/

  deploy:
    runs-on: ubuntu-latest
    needs: [quality, e2e]
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - run: pnpm install --frozen-lockfile
      - run: pnpm db:migrate    # run migrations before deploy
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
      - uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

### Docker for Non-Vercel Deployments

```dockerfile
# Dockerfile — multi-stage for minimal production image
FROM node:20-alpine AS base
RUN corepack enable pnpm

FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
RUN addgroup --system --gid 1001 nodejs && adduser --system --uid 1001 nextjs
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static
COPY --from=builder /app/public ./public
USER nextjs
EXPOSE 3000
CMD ["node", "server.js"]
```

### Environment Management

```
Branch → Environment mapping:
main → production (vercel.com domain)
staging → staging (staging.yourdomain.com, preview deployment)
feature/* → PR preview (auto-deployed by Vercel)

Database per environment:
- production: primary Neon/Supabase project
- staging: separate Neon project or Neon branch from prod
- PR previews: Neon database branching (git branch = DB branch — zero cost, instant)
- Local dev: Docker postgres OR personal Neon branch
```

---

## 12. Observability

### Setup (OpenTelemetry + Sentry)

```typescript
// instrumentation.ts (Next.js 15 — loaded before app)
export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    const { NodeSDK } = await import('@opentelemetry/sdk-node')
    const { getNodeAutoInstrumentations } = await import('@opentelemetry/auto-instrumentations-node')
    const { OTLPTraceExporter } = await import('@opentelemetry/exporter-trace-otlp-http')

    const sdk = new NodeSDK({
      traceExporter: new OTLPTraceExporter({ url: process.env.OTEL_EXPORTER_OTLP_ENDPOINT }),
      instrumentations: [getNodeAutoInstrumentations()],
    })
    sdk.start()
  }
}
```

```typescript
// sentry.server.config.ts
import * as Sentry from '@sentry/nextjs'

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampleRate: process.env.NODE_ENV === 'production' ? 0.1 : 1.0,
  environment: process.env.NODE_ENV,
  integrations: [
    Sentry.openTelemetryIntegration(), // Sentry picks up OTEL spans automatically
  ],
  beforeSend(event) {
    // Scrub PII before sending
    if (event.user) delete event.user.email
    return event
  },
})
```

### Structured Logging (Pino)

```typescript
// src/lib/logger.ts
import pino from 'pino'

export const logger = pino({
  level: process.env.LOG_LEVEL ?? 'info',
  base: { service: 'web', env: process.env.NODE_ENV },
  ...(process.env.NODE_ENV === 'development' && {
    transport: { target: 'pino-pretty' },
  }),
})

// Usage — always structured, never console.log in production:
logger.info({ userId, action: 'post.created', postId }, 'Post created')
logger.error({ err, userId }, 'Failed to create post')
```

---

## 13. AI Integration Patterns

### Vercel AI SDK v6 (Streaming)

```typescript
// app/api/chat/route.ts — streaming chat endpoint
import { streamText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'
import { z } from 'zod'
import { auth } from '@/server/auth'

export async function POST(req: Request) {
  const session = await auth()
  if (!session?.user) return new Response('Unauthorized', { status: 401 })

  const { messages } = await req.json()

  const result = streamText({
    model: anthropic('claude-sonnet-4-5'),
    system: 'You are a helpful assistant.',
    messages,
    tools: {
      searchDocs: {
        description: 'Search the knowledge base',
        parameters: z.object({ query: z.string() }),
        execute: async ({ query }) => searchVectorStore(query),
      },
    },
    maxSteps: 5,
    onFinish: async ({ usage, finishReason }) => {
      // Track usage for billing/analytics
      await trackUsage({ userId: session.user.id, ...usage })
    },
  })

  return result.toDataStreamResponse()
}

// components/chat.tsx — client component
'use client'
import { useChat } from 'ai/react'

export function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: '/api/chat',
    onError: (err) => toast.error(err.message),
  })

  return (
    <div>
      {messages.map((m) => (
        <div key={m.id}>{m.content}</div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} disabled={isLoading} />
        <button type="submit">Send</button>
      </form>
    </div>
  )
}
```

### RAG Pattern

```typescript
// RAG pipeline: Embed → Store → Retrieve → Augment → Generate
// src/server/rag/pipeline.ts
import { openai } from '@ai-sdk/openai'
import { embed, embedMany } from 'ai'
import { db } from '../db'
import { documents } from '../db/schema/documents'
import { cosineDistance, gt, sql } from 'drizzle-orm'

// 1. Ingest: chunk and embed documents
export async function ingestDocument(text: string, metadata: Record<string, unknown>) {
  const chunks = chunkText(text, { chunkSize: 512, overlap: 64 })
  const { embeddings } = await embedMany({
    model: openai.embedding('text-embedding-3-small'),
    values: chunks,
  })

  await db.insert(documents).values(
    chunks.map((chunk, i) => ({
      content: chunk,
      embedding: embeddings[i],   // stored as vector(1536) in Postgres with pgvector
      metadata,
    }))
  )
}

// 2. Retrieve: semantic search
export async function retrieveContext(query: string, topK = 5) {
  const { embedding } = await embed({
    model: openai.embedding('text-embedding-3-small'),
    value: query,
  })

  const similarity = sql<number>`1 - (${cosineDistance(documents.embedding, embedding)})`

  return db
    .select({ content: documents.content, similarity })
    .from(documents)
    .where(gt(similarity, 0.7))
    .orderBy(desc(similarity))
    .limit(topK)
}

// 3. Augment the prompt in the chat endpoint
const context = await retrieveContext(lastUserMessage)
const augmentedSystem = `${baseSystem}\n\nContext:\n${context.map(c => c.content).join('\n\n')}`
```

---

## 14. Mobile (Expo + React Native)

### Shared Code Strategy

```
Strategy: share as much TypeScript as possible

packages/
├─ validators/   # Zod schemas — 100% shared
├─ types/        # TypeScript interfaces — 100% shared
└─ api-client/   # tRPC client or fetch wrappers — 100% shared

The web and mobile apps call the SAME tRPC router — no separate mobile API.
Auth tokens for mobile use Better Auth's mobile session endpoints.
```

### Expo App Structure (2025)

```typescript
// Use Expo Router (file-based routing) — matches Next.js mental model
app/
├─ (auth)/
│   ├─ login.tsx
│   └─ register.tsx
├─ (tabs)/
│   ├─ _layout.tsx    # Tab navigator
│   ├─ index.tsx      # Home tab
│   └─ profile.tsx    # Profile tab
└─ _layout.tsx        # Root layout with auth guard

// NativeWind v4 for Tailwind-in-React-Native
// New Architecture is always on from RN 0.82 — no legacy bridge
// Expo DOM Components for gradual web-to-native migration
```

### Mobile-Specific Considerations

```
State:          TanStack Query works identically (same hooks as web)
Offline:        MMKV for fast local storage; WatermelonDB for offline-first DB
Navigation:     Expo Router (file-based) — consistent with Next.js mental model
Push notifs:    Expo Notifications (handles FCM + APNs unified)
OTA updates:    Expo Updates — ship JS changes without app store review
Native modules: Expo Modules API > legacy bridge (works with New Architecture)
```

---

## 15. Common Full Stack Patterns (Copy-Paste Ready)

### Pagination (Cursor-Based)

```typescript
// Server
export const listProcedure = publicProcedure
  .input(z.object({ cursor: z.string().optional(), limit: z.number().min(1).max(50).default(20) }))
  .query(async ({ input }) => {
    const items = await db.query.posts.findMany({
      where: input.cursor ? gt(posts.id, input.cursor) : undefined,
      limit: input.limit + 1,
      orderBy: asc(posts.id),
    })
    const hasMore = items.length > input.limit
    return {
      items: items.slice(0, input.limit),
      nextCursor: hasMore ? items[input.limit - 1].id : undefined,
    }
  })

// Client — infinite scroll with TanStack Query
const { data, fetchNextPage, hasNextPage } = trpc.post.list.useInfiniteQuery(
  { limit: 20 },
  { getNextPageParam: (last) => last.nextCursor }
)
```

### File Upload (Direct to R2/S3)

```typescript
// 1. Client requests a presigned URL from the server
// 2. Client uploads directly to R2 — server never touches the bytes
// 3. Client tells server the upload is complete

// Server — generate presigned URL
export const getUploadUrl = protectedProcedure
  .input(z.object({ filename: z.string(), contentType: z.string(), size: z.number().max(10_000_000) }))
  .mutation(async ({ ctx, input }) => {
    const key = `${ctx.session.user.id}/${createId()}-${input.filename}`
    const url = await getSignedUploadUrl({ key, contentType: input.contentType })
    return { url, key }
  })

// Client
async function uploadFile(file: File) {
  const { url, key } = await trpc.getUploadUrl.mutate({
    filename: file.name,
    contentType: file.type,
    size: file.size,
  })
  await fetch(url, { method: 'PUT', body: file, headers: { 'Content-Type': file.type } })
  return key
}
```

### Background Jobs

```typescript
// Use Trigger.dev (v3) for durable background jobs
// tasks/send-welcome-email.ts
import { task } from '@trigger.dev/sdk/v3'
import { sendEmail } from '@/server/email'

export const sendWelcomeEmail = task({
  id: 'send-welcome-email',
  retry: { maxAttempts: 3, factor: 2, minTimeoutInMs: 1000 },
  run: async (payload: { userId: string; email: string }) => {
    await sendEmail({
      to: payload.email,
      subject: 'Welcome!',
      react: <WelcomeEmail userId={payload.userId} />,
    })
  },
})

// Trigger from server action
await sendWelcomeEmail.trigger({ userId: user.id, email: user.email })
```

### Multi-Tenant Architecture

```typescript
// Organization-scoped queries with RLS
// Every table that belongs to an org has orgId column
// Middleware injects the current org into tRPC context

const isOrgMember = t.middleware(async ({ ctx, next }) => {
  const orgId = ctx.req.headers.get('x-org-id')
  const membership = await db.query.orgMembers.findFirst({
    where: and(
      eq(orgMembers.orgId, orgId),
      eq(orgMembers.userId, ctx.session.user.id)
    ),
  })
  if (!membership) throw new TRPCError({ code: 'FORBIDDEN' })
  return next({ ctx: { ...ctx, orgId, role: membership.role } })
})

export const orgProcedure = protectedProcedure.use(isOrgMember)
// All org-scoped procedures automatically scope by ctx.orgId
```

---

## 16. Quality Standards

Every feature shipped must pass:

```
□ TypeScript strict mode — zero `any`, zero ts-ignore without comment
□ Zod validation on all inputs (server actions, API routes, tRPC procedures)
□ Auth check in every server-side mutation
□ Unit tests for business logic
□ Vitest coverage ≥ 80% for src/server/services/
□ Playwright E2E test for new critical user journeys
□ Lighthouse CI — no Core Web Vitals regression
□ pnpm audit — no high/critical vulnerabilities
□ No secrets in code (use env.ts + t3-env)
□ Sentry error monitoring configured
□ Structured logs (Pino) — no console.log in production paths
□ Security headers passing securityheaders.com check
□ Bundle analyzer run — no unexpected size increases
```

---

## 17. Principal Engineer Decision Heuristics

These are the questions to ask — and the answers that should guide decisions:

**"Should I add another package/library?"**
Can I solve this with 20 lines of code? If yes, write the code. If the library does something genuinely hard (crypto, PDF generation, payments), use it.

**"Should I cache this?"**
Measure first. Most apps aren't cache-limited; most bugs come from stale caches. Cache when you have data showing you need it.

**"Should I use microservices?"**
No, unless you have multiple independent teams that need to deploy independently, or truly orthogonal scaling requirements. Start with a modular monolith. Extract services when the pain of the monolith exceeds the pain of distribution.

**"Should I build my own [auth/upload/email/payments]?"**
No. Use Better Auth, R2, Resend, Stripe. These are not differentiators. Your product logic is.

**"Is this over-engineered?"**
Ask: what is the simplest possible thing that works correctly and can be extended later? If what you're building is more complex than that, cut it.

**"Should I use Next.js Server Actions or tRPC?"**
Server Actions for forms and page-level mutations (first-class React 19 integration, no client state needed). tRPC for data-fetching APIs, mobile clients, or anywhere you need client-side caching via TanStack Query.
