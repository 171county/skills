---
name: full-stack-app-expert
description: Expert-level full-stack application development covering Next.js 15 App Router, TypeScript advanced patterns, state management, backend frameworks, databases, authentication, deployment, AI integration, testing, performance, and security for production systems.
---

# Full-Stack Application Expert

## Foundational Architecture Mental Model

Full-stack expertise in 2025-2026 means understanding the render/execution boundary clearly: **where does code run, when, and with what access?** The React Server Components model redefines this boundary — components execute on the server by default, client components opt in with `"use client"`. This changes how you think about data fetching, bundle size, and security.

The stack that has converged as the production standard for AI-era applications:
- **Frontend**: Next.js 15 (App Router) + TypeScript 5.x + Tailwind CSS 4
- **State**: TanStack Query v5 (server state) + Zustand (client state)
- **Backend API**: tRPC (type-safe) or Hono (REST/edge) or Next.js Route Handlers
- **Database**: Drizzle ORM + PostgreSQL (Neon/Supabase) or SQLite (Turso/Cloudflare D1)
- **Auth**: Better Auth or Clerk
- **AI**: Vercel AI SDK 4/5
- **Deployment**: Vercel (frontend) / Railway or Fly.io (dedicated backend)
- **Testing**: Vitest + React Testing Library + Playwright

---

## Next.js 15 App Router Mastery

### Server Components by Default

React Server Components (RSC) execute on the server and never hydrate to the client. They can be `async`, access databases directly, and keep secrets server-side. Their output is a serialized React tree (RSC payload), not HTML.

```tsx
// app/dashboard/page.tsx - Server Component (default)
import { db } from '@/lib/db'
import { auth } from '@/lib/auth'

export default async function DashboardPage() {
  const session = await auth()
  if (!session) redirect('/login')

  // Direct DB access - no API round trip
  const metrics = await db.query.metrics.findMany({
    where: eq(metrics.userId, session.user.id),
    orderBy: desc(metrics.createdAt),
    limit: 10,
  })

  return <MetricsDashboard metrics={metrics} />
}
```

**RSC vs Client Component decision tree**:
| Need | Use |
|------|-----|
| Database/secrets access | Server Component |
| Event handlers (onClick) | Client Component |
| Browser APIs (localStorage, window) | Client Component |
| `useState`, `useEffect` | Client Component |
| Large dependencies (charts, editors) | Client Component |
| Static or dynamic data display | Server Component |

### Server Actions

Server Actions replace API routes for mutations. They run on the server, co-located with components, with built-in CSRF protection.

```tsx
// app/actions/posts.ts
'use server'

import { db } from '@/lib/db'
import { auth } from '@/lib/auth'
import { revalidatePath } from 'next/cache'
import { z } from 'zod'

const CreatePostSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(10),
})

export async function createPost(formData: FormData) {
  const session = await auth()
  if (!session) throw new Error('Unauthorized')

  const parsed = CreatePostSchema.safeParse({
    title: formData.get('title'),
    content: formData.get('content'),
  })

  if (!parsed.success) {
    return { error: parsed.error.flatten() }
  }

  const post = await db.insert(posts).values({
    ...parsed.data,
    authorId: session.user.id,
  }).returning()

  revalidatePath('/posts')
  return { success: true, post: post[0] }
}

// Component using the action
'use client'
import { useActionState } from 'react'
import { createPost } from '@/app/actions/posts'

export function CreatePostForm() {
  const [state, action, pending] = useActionState(createPost, null)

  return (
    <form action={action}>
      <input name="title" required />
      <textarea name="content" required />
      {state?.error && <ErrorDisplay error={state.error} />}
      <button disabled={pending}>
        {pending ? 'Creating...' : 'Create Post'}
      </button>
    </form>
  )
}
```

### Caching Architecture

Next.js 15 changed caching defaults — fetch is **no longer cached by default** (reverting Next.js 13/14 behavior). Explicit cache control is now required.

```tsx
// Cached fetch (Static data)
const data = await fetch('/api/config', {
  next: { revalidate: 3600 } // ISR: revalidate every hour
})

// Never cached (Dynamic data)
const data = await fetch('/api/user', {
  cache: 'no-store'
})

// On-demand revalidation via tags
const posts = await fetch('/api/posts', {
  next: { tags: ['posts'] }
})

// In a Server Action after mutation:
import { revalidateTag } from 'next/cache'
revalidateTag('posts')
```

**`unstable_cache` for database queries**:
```tsx
import { unstable_cache } from 'next/cache'

export const getCachedUser = unstable_cache(
  async (userId: string) => {
    return db.query.users.findFirst({ where: eq(users.id, userId) })
  },
  ['user'],
  { revalidate: 60, tags: ['users'] }
)
```

### Partial Prerendering (PPR)

PPR lets a single route have both static (pre-rendered) and dynamic (streamed) parts. The static shell is served from CDN instantly; dynamic parts stream in.

```tsx
// next.config.ts
const nextConfig = {
  experimental: {
    ppr: 'incremental', // opt routes in individually
  }
}

// app/product/[id]/page.tsx
export const experimental_ppr = true

import { Suspense } from 'react'

export default function ProductPage({ params }: { params: { id: string } }) {
  return (
    <div>
      {/* Static - pre-rendered at build time */}
      <ProductLayout />
      
      {/* Dynamic - streamed on request */}
      <Suspense fallback={<PriceSkeleton />}>
        <DynamicPrice productId={params.id} />
      </Suspense>
      
      <Suspense fallback={<ReviewsSkeleton />}>
        <DynamicReviews productId={params.id} />
      </Suspense>
    </div>
  )
}
```

### Route Handlers and Middleware

```typescript
// app/api/webhooks/stripe/route.ts
import { NextRequest, NextResponse } from 'next/server'
import Stripe from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

export async function POST(req: NextRequest) {
  const body = await req.text()
  const sig = req.headers.get('stripe-signature')!

  let event: Stripe.Event
  try {
    event = stripe.webhooks.constructEvent(body, sig, process.env.STRIPE_WEBHOOK_SECRET!)
  } catch {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 400 })
  }

  switch (event.type) {
    case 'checkout.session.completed':
      await handleCheckoutCompleted(event.data.object)
      break
  }

  return NextResponse.json({ received: true })
}

// middleware.ts - runs on every request, edge runtime
import { NextMiddleware, NextResponse } from 'next/server'

export const middleware: NextMiddleware = async (req) => {
  const session = await getSession(req)

  if (req.nextUrl.pathname.startsWith('/dashboard') && !session) {
    return NextResponse.redirect(new URL('/login', req.url))
  }

  // Security headers
  const response = NextResponse.next()
  response.headers.set('X-Frame-Options', 'DENY')
  response.headers.set('X-Content-Type-Options', 'nosniff')
  response.headers.set('Referrer-Policy', 'strict-origin-when-cross-origin')
  return response
}

export const config = {
  matcher: ['/((?!_next/static|_next/image|favicon.ico).*)']
}
```

---

## TypeScript Advanced Patterns

### Discriminated Unions

The cornerstone of type-safe state machines and API responses:

```typescript
type ApiResult<T> =
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: string; code: number }

function handleResult<T>(result: ApiResult<T>) {
  switch (result.status) {
    case 'loading':
      return <Spinner />
    case 'success':
      return <DataView data={result.data} />  // TypeScript knows .data exists
    case 'error':
      return <ErrorView error={result.error} code={result.code} />
  }
}
```

### Branded Types

Prevent primitive obsession — distinguish IDs of different entity types at compile time:

```typescript
type Brand<T, B extends string> = T & { readonly _brand: B }

type UserId = Brand<string, 'UserId'>
type PostId = Brand<string, 'PostId'>
type Email = Brand<string, 'Email'>

// Construction functions with runtime validation
function createUserId(id: string): UserId {
  if (!id.startsWith('usr_')) throw new Error('Invalid user ID format')
  return id as UserId
}

function createEmail(email: string): Email {
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) throw new Error('Invalid email')
  return email as Email
}

// Type error: cannot pass PostId where UserId expected
function getUser(id: UserId) { ... }
const postId = 'pst_123' as PostId
getUser(postId) // TypeScript Error!
```

### Template Literal Types

```typescript
type EventName = `on${Capitalize<string>}`
type CSSProperty = `${string}-${string}`
type Route = `/api/${string}`

// Powerful: typed event system
type EventMap = {
  click: MouseEvent
  focus: FocusEvent
  input: InputEvent
}

type EventHandler<T extends keyof EventMap> = (event: EventMap[T]) => void

function on<T extends keyof EventMap>(
  event: T,
  handler: EventHandler<T>
): void { ... }
```

### Const Type Parameters (TypeScript 5.0+)

```typescript
// Without const: inferred as string[]
function makeArray<T>(items: T[]): T[] { return items }
const arr = makeArray(['a', 'b']) // string[]

// With const: inferred as tuple literal
function makeArray<const T>(items: T): T { return items }
const arr = makeArray(['a', 'b'] as const) // readonly ['a', 'b']

// Powerful for config objects
function createRoutes<const T extends Record<string, string>>(routes: T): T {
  return routes
}

const routes = createRoutes({
  home: '/',
  dashboard: '/dashboard',
  settings: '/settings',
})
// routes.home is typed as '/', not string
```

### `NoInfer` Utility Type (TypeScript 5.4+)

```typescript
// Before NoInfer: TypeScript uses the default to influence T inference
function createState<T>(initial: T, fallback: T): [T, T] {
  return [initial, fallback]
}

// After NoInfer: fallback can't influence the inference of T
function createState<T>(initial: T, fallback: NoInfer<T>): [T, T] {
  return [initial, fallback]
}

// Practical: constrain options to inferred type
function select<T>(options: T[], selected: NoInfer<T>): T {
  return options.includes(selected) ? selected : options[0]
}
```

### Satisfies Operator

```typescript
type Colors = Record<string, [number, number, number] | string>

// satisfies validates the shape without widening the type
const palette = {
  red: [255, 0, 0],
  green: '#00ff00',
  blue: [0, 0, 255],
} satisfies Colors

// palette.red is [number, number, number], NOT [number, number, number] | string
palette.red.map(...)  // OK - TypeScript knows it's a tuple
```

---

## State Management

### TanStack Query v5 (Server State)

TanStack Query v5 introduced a unified API removing overloads, improving TypeScript inference, and adding fine-grained subscriptions.

```typescript
// Queries
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

function useUser(userId: string) {
  return useQuery({
    queryKey: ['user', userId],
    queryFn: () => api.getUser(userId),
    staleTime: 5 * 60 * 1000,  // 5 minutes
    gcTime: 30 * 60 * 1000,    // 30 minutes (was cacheTime in v4)
    enabled: !!userId,
  })
}

// Mutations with optimistic updates
function useUpdateUser() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: (data: UpdateUserData) => api.updateUser(data),
    onMutate: async (newData) => {
      // Cancel in-flight queries
      await queryClient.cancelQueries({ queryKey: ['user', newData.id] })
      
      // Snapshot for rollback
      const previousUser = queryClient.getQueryData(['user', newData.id])
      
      // Optimistic update
      queryClient.setQueryData(['user', newData.id], (old: User) => ({
        ...old,
        ...newData,
      }))
      
      return { previousUser }
    },
    onError: (_, newData, context) => {
      queryClient.setQueryData(['user', newData.id], context?.previousUser)
    },
    onSettled: (_, __, newData) => {
      queryClient.invalidateQueries({ queryKey: ['user', newData.id] })
    },
  })
}

// Infinite queries for pagination
function usePosts() {
  return useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: ({ pageParam }) => api.getPosts({ cursor: pageParam }),
    initialPageParam: undefined as string | undefined,
    getNextPageParam: (lastPage) => lastPage.nextCursor,
  })
}

// queryOptions factory pattern (v5)
const userQueryOptions = (userId: string) => queryOptions({
  queryKey: ['user', userId],
  queryFn: () => api.getUser(userId),
})

// Reuse in loader + component:
// loader: queryClient.ensureQueryData(userQueryOptions(id))
// component: useQuery(userQueryOptions(id))
```

### Zustand (Client State)

```typescript
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'

interface UIStore {
  sidebarOpen: boolean
  theme: 'light' | 'dark'
  notifications: Notification[]
  
  toggleSidebar: () => void
  setTheme: (theme: 'light' | 'dark') => void
  addNotification: (notification: Notification) => void
  removeNotification: (id: string) => void
}

export const useUIStore = create<UIStore>()(
  persist(
    immer((set) => ({
      sidebarOpen: true,
      theme: 'light',
      notifications: [],

      toggleSidebar: () => set((state) => { state.sidebarOpen = !state.sidebarOpen }),
      setTheme: (theme) => set({ theme }),
      addNotification: (notification) => set((state) => {
        state.notifications.push(notification)
      }),
      removeNotification: (id) => set((state) => {
        state.notifications = state.notifications.filter(n => n.id !== id)
      }),
    })),
    {
      name: 'ui-store',
      storage: createJSONStorage(() => localStorage),
      partialize: (state) => ({ theme: state.theme }), // only persist theme
    }
  )
)

// Selective subscriptions to prevent re-renders
const theme = useUIStore((state) => state.theme)
const toggleSidebar = useUIStore((state) => state.toggleSidebar)
```

### Jotai (Atomic State)

Jotai is ideal for fine-grained, derived state without selector boilerplate:

```typescript
import { atom, useAtom, useAtomValue, useSetAtom } from 'jotai'
import { atomWithStorage, atomWithQuery } from 'jotai/utils'

// Primitive atoms
const countAtom = atom(0)
const textAtom = atomWithStorage('text', '')  // persists to localStorage

// Derived read-only atom
const doubleCountAtom = atom((get) => get(countAtom) * 2)

// Derived read-write atom
const upperTextAtom = atom(
  (get) => get(textAtom).toUpperCase(),
  (get, set, value: string) => set(textAtom, value.toLowerCase())
)

// Async atom
const userAtom = atom(async (get) => {
  const id = get(selectedUserIdAtom)
  return fetch(`/api/users/${id}`).then(r => r.json())
})

// Usage
function Counter() {
  const [count, setCount] = useAtom(countAtom)
  const double = useAtomValue(doubleCountAtom)
  const setCount = useSetAtom(countAtom) // no re-render on count change
  
  return <button onClick={() => setCount(c => c + 1)}>{count} (×2: {double})</button>
}
```

---

## Backend Frameworks

### Hono (Edge-First)

Hono runs on Node.js, Cloudflare Workers, Deno, Bun — write once, deploy anywhere:

```typescript
import { Hono } from 'hono'
import { zValidator } from '@hono/zod-validator'
import { jwt } from 'hono/jwt'
import { rateLimiter } from 'hono-rate-limiter'
import { z } from 'zod'

const app = new Hono()

// Middleware
app.use('/api/*', jwt({ secret: process.env.JWT_SECRET! }))
app.use(rateLimiter({
  windowMs: 60 * 1000,
  limit: 100,
  keyGenerator: (c) => c.req.header('x-forwarded-for') ?? 'unknown',
}))

const PostSchema = z.object({
  title: z.string().min(1),
  content: z.string().min(10),
})

app.post('/api/posts', zValidator('json', PostSchema), async (c) => {
  const data = c.req.valid('json')
  const jwtPayload = c.get('jwtPayload')
  
  const post = await db.insert(posts)
    .values({ ...data, authorId: jwtPayload.sub })
    .returning()
  
  return c.json(post[0], 201)
})

// RPC client type sharing
export type AppType = typeof app
```

Hono RPC for end-to-end type safety without tRPC:
```typescript
// Client
import { hc } from 'hono/client'
import type { AppType } from './server'

const client = hc<AppType>('http://localhost:3000')

// Fully typed:
const post = await client.api.posts.$post({ json: { title: '...', content: '...' } })
```

### Fastify (Node.js Performance)

```typescript
import Fastify from 'fastify'
import { TypeBoxTypeProvider } from '@fastify/type-provider-typebox'
import { Type } from '@sinclair/typebox'

const fastify = Fastify({ logger: true }).withTypeProvider<TypeBoxTypeProvider>()

await fastify.register(import('@fastify/rate-limit'), {
  max: 100,
  timeWindow: '1 minute',
})

const CreatePostBody = Type.Object({
  title: Type.String({ minLength: 1, maxLength: 200 }),
  content: Type.String({ minLength: 10 }),
})

fastify.post('/posts', {
  schema: {
    body: CreatePostBody,
    response: {
      201: Type.Object({
        id: Type.String(),
        title: Type.String(),
      })
    }
  }
}, async (request, reply) => {
  const { title, content } = request.body  // fully typed
  const post = await db.createPost({ title, content })
  return reply.code(201).send(post)
})
```

### tRPC (End-to-End Type Safety)

```typescript
// server/trpc.ts
import { initTRPC, TRPCError } from '@trpc/server'
import { z } from 'zod'

const t = initTRPC.context<Context>().create()
export const router = t.router
export const publicProcedure = t.procedure
export const protectedProcedure = t.procedure.use(({ ctx, next }) => {
  if (!ctx.session) throw new TRPCError({ code: 'UNAUTHORIZED' })
  return next({ ctx: { ...ctx, session: ctx.session } })
})

// server/routers/posts.ts
export const postsRouter = router({
  list: publicProcedure
    .input(z.object({ cursor: z.string().optional(), limit: z.number().max(50).default(20) }))
    .query(async ({ input }) => {
      return db.query.posts.findMany({
        take: input.limit + 1,
        cursor: input.cursor ? { id: input.cursor } : undefined,
      })
    }),

  create: protectedProcedure
    .input(z.object({ title: z.string().min(1), content: z.string().min(10) }))
    .mutation(async ({ ctx, input }) => {
      return db.insert(posts).values({ ...input, authorId: ctx.session.user.id }).returning()
    }),
})

// Client usage (fully typed, no codegen)
const { data, fetchNextPage } = trpc.posts.list.useInfiniteQuery(
  { limit: 20 },
  { getNextPageParam: (lastPage) => lastPage.nextCursor }
)

const createPost = trpc.posts.create.useMutation({
  onSuccess: () => utils.posts.list.invalidate(),
})
```

---

## Database Layer

### Drizzle ORM

Drizzle is the 2025 standard: SQL-first, fully typed, ~7.4KB bundle, zero dependencies:

```typescript
// schema/posts.ts
import { pgTable, text, timestamp, uuid, integer, index } from 'drizzle-orm/pg-core'
import { users } from './users'
import { relations } from 'drizzle-orm'

export const posts = pgTable('posts', {
  id: uuid('id').defaultRandom().primaryKey(),
  title: text('title').notNull(),
  content: text('content').notNull(),
  authorId: uuid('author_id').notNull().references(() => users.id, { onDelete: 'cascade' }),
  viewCount: integer('view_count').notNull().default(0),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().$onUpdate(() => new Date()).notNull(),
}, (table) => ({
  authorIdx: index('posts_author_id_idx').on(table.authorId),
  createdIdx: index('posts_created_at_idx').on(table.createdAt),
}))

export const postsRelations = relations(posts, ({ one, many }) => ({
  author: one(users, { fields: [posts.authorId], references: [users.id] }),
  comments: many(comments),
}))

// db/index.ts
import { drizzle } from 'drizzle-orm/neon-http'
import { neon } from '@neondatabase/serverless'
import * as schema from './schema'

const sql = neon(process.env.DATABASE_URL!)
export const db = drizzle(sql, { schema })

// Queries
const postsWithAuthors = await db.query.posts.findMany({
  with: { author: { columns: { id: true, name: true, avatar: true } } },
  where: eq(posts.authorId, userId),
  orderBy: desc(posts.createdAt),
  limit: 20,
})

// Transactions
await db.transaction(async (tx) => {
  const [post] = await tx.insert(posts).values(postData).returning()
  await tx.update(users)
    .set({ postCount: sql`${users.postCount} + 1` })
    .where(eq(users.id, postData.authorId))
  return post
})
```

**Drizzle vs Prisma 7 Decision**:
| Factor | Drizzle | Prisma 7 |
|--------|---------|----------|
| Bundle size | ~7.4KB | ~50KB+ |
| Query API | SQL-like, explicit | Higher-level abstraction |
| Type inference | From schema | From schema |
| Migrations | `drizzle-kit` | `prisma migrate` |
| Edge runtime | Yes (neon-http, d1) | Limited |
| Raw SQL | Natural | `$queryRaw` escape hatch |
| Learning curve | SQL knowledge needed | More intuitive |
| When to use | Edge, size-critical, SQL expertise | Rapid prototyping, complex relations |

### Redis Patterns

```typescript
import { Redis } from '@upstash/redis'

const redis = Redis.fromEnv()

// Cache-aside pattern
async function getCachedData<T>(
  key: string,
  fetcher: () => Promise<T>,
  ttl = 300
): Promise<T> {
  const cached = await redis.get<T>(key)
  if (cached) return cached

  const data = await fetcher()
  await redis.setex(key, ttl, data)
  return data
}

// Rate limiting with sliding window
async function checkRateLimit(identifier: string, limit: number, window: number) {
  const key = `rl:${identifier}`
  const now = Date.now()
  const windowStart = now - window * 1000

  const pipeline = redis.pipeline()
  pipeline.zremrangebyscore(key, 0, windowStart)
  pipeline.zadd(key, { score: now, member: now.toString() })
  pipeline.zcard(key)
  pipeline.expire(key, window)

  const results = await pipeline.exec()
  const count = results[2] as number
  return { allowed: count <= limit, count, remaining: Math.max(0, limit - count) }
}

// Pub/Sub for real-time
const publisher = Redis.fromEnv()
const subscriber = Redis.fromEnv()

await publisher.publish('channel', JSON.stringify({ type: 'update', data }))
await subscriber.subscribe('channel', (message) => {
  const event = JSON.parse(message)
  // handle event
})
```

---

## Authentication

### Better Auth

Better Auth is the 2025 framework-agnostic authentication library for TypeScript:

```typescript
// auth.ts (server)
import { betterAuth } from 'better-auth'
import { drizzleAdapter } from 'better-auth/adapters/drizzle'
import { nextCookies } from 'better-auth/next-js'

export const auth = betterAuth({
  database: drizzleAdapter(db, {
    provider: 'pg',
    schema: { user: users, session: sessions, account: accounts, verification: verifications },
  }),
  plugins: [nextCookies()],
  
  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true,
  },
  
  socialProviders: {
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    },
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
  },
  
  session: {
    expiresIn: 60 * 60 * 24 * 7, // 7 days
    updateAge: 60 * 60 * 24,      // refresh if older than 1 day
  },
})

// Route handler
export { GET, POST } from './auth.handler'

// Client
import { createAuthClient } from 'better-auth/react'
const authClient = createAuthClient({ baseURL: process.env.NEXT_PUBLIC_APP_URL! })

const { data: session } = authClient.useSession()
await authClient.signIn.email({ email, password, callbackURL: '/dashboard' })
await authClient.signIn.social({ provider: 'github', callbackURL: '/dashboard' })
await authClient.signOut()
```

### JWT Best Practices

```typescript
import { SignJWT, jwtVerify } from 'jose'

const secret = new TextEncoder().encode(process.env.JWT_SECRET!)

export async function createToken(payload: JWTPayload, expiresIn = '7d') {
  return new SignJWT(payload)
    .setProtectedHeader({ alg: 'HS256' })
    .setIssuedAt()
    .setExpirationTime(expiresIn)
    .setJti(crypto.randomUUID())
    .sign(secret)
}

export async function verifyToken(token: string): Promise<JWTPayload> {
  const { payload } = await jwtVerify(token, secret)
  return payload as JWTPayload
}

// httpOnly cookie storage (XSS-resistant)
response.cookies.set('token', token, {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'lax',
  maxAge: 60 * 60 * 24 * 7, // 7 days
  path: '/',
})
```

---

## AI Integration with Vercel AI SDK

The Vercel AI SDK is the standard for integrating LLMs into full-stack apps. Version 4/5 unifies the API across providers and runtimes.

### Core Patterns

```typescript
import { streamText, generateObject, generateText, tool } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'
import { openai } from '@ai-sdk/openai'
import { z } from 'zod'

// Streaming chat
// app/api/chat/route.ts
export async function POST(req: Request) {
  const { messages } = await req.json()

  const result = streamText({
    model: anthropic('claude-sonnet-4-6'),
    system: 'You are a helpful assistant.',
    messages,
    maxTokens: 4096,
    tools: {
      getWeather: tool({
        description: 'Get current weather for a location',
        parameters: z.object({
          location: z.string().describe('City and country, e.g. "London, UK"'),
        }),
        execute: async ({ location }) => {
          return { temperature: 20, condition: 'sunny', location }
        },
      }),
    },
    maxSteps: 5, // allow multi-step tool use
  })

  return result.toDataStreamResponse()
}

// Client component
'use client'
import { useChat } from 'ai/react'

export function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: '/api/chat',
    onError: (error) => console.error(error),
  })

  return (
    <div>
      {messages.map((m) => (
        <div key={m.id} className={m.role === 'user' ? 'text-right' : 'text-left'}>
          {m.content}
          {m.toolInvocations?.map((ti) => (
            <ToolDisplay key={ti.toolCallId} invocation={ti} />
          ))}
        </div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} />
        <button disabled={isLoading}>Send</button>
      </form>
    </div>
  )
}

// Structured generation
const ProductSchema = z.object({
  name: z.string(),
  price: z.number().positive(),
  category: z.enum(['electronics', 'clothing', 'food']),
  tags: z.array(z.string()).max(5),
})

const { object: product } = await generateObject({
  model: openai('gpt-4o'),
  schema: ProductSchema,
  prompt: 'Generate a product listing for a wireless keyboard',
})
// product is fully typed as z.infer<typeof ProductSchema>
```

### Streaming with useObject

```typescript
'use client'
import { experimental_useObject as useObject } from 'ai/react'

function ProductGenerator() {
  const { object, submit, isLoading } = useObject({
    api: '/api/generate-product',
    schema: ProductSchema,
  })

  return (
    <div>
      <button onClick={() => submit({ prompt: 'laptop' })}>Generate</button>
      {object?.name && <p>Name: {object.name}</p>}
      {object?.price && <p>Price: ${object.price}</p>}
    </div>
  )
}
```

### RAG Pipeline Integration

```typescript
import { embed, embedMany } from 'ai'
import { openai } from '@ai-sdk/openai'

// Embedding generation
const { embedding } = await embed({
  model: openai.embedding('text-embedding-3-large'),
  value: 'What is the capital of France?',
})

// Batch embedding
const { embeddings } = await embedMany({
  model: openai.embedding('text-embedding-3-large'),
  values: chunks.map(c => c.text),
})

// Vector similarity search (with pgvector)
const queryEmbedding = await embed({
  model: openai.embedding('text-embedding-3-small'),
  value: userQuery,
})

const relevantDocs = await db.execute(sql`
  SELECT content, 1 - (embedding <=> ${JSON.stringify(queryEmbedding.embedding)}::vector) as similarity
  FROM documents
  WHERE 1 - (embedding <=> ${JSON.stringify(queryEmbedding.embedding)}::vector) > 0.7
  ORDER BY similarity DESC
  LIMIT 5
`)
```

---

## Testing Strategy

### Testing Trophy (Not Pyramid)

The Testing Trophy prioritizes **integration tests** over unit tests:

```
        /\ E2E /\        (few, high confidence)
       /  Smoke  \
      /           \
     / Integration \      (most tests, best ROI)
    /_______________\
   /   Unit Tests   \     (pure functions, utils)
  /_________________\
```

**Unit tests**: Pure functions, complex business logic, utility functions.
**Integration tests**: API routes, database interactions, component trees with providers.
**E2E tests**: Critical user journeys (signup, checkout, core feature).

### Vitest + React Testing Library

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'
import { resolve } from 'path'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    globals: true,
    coverage: {
      provider: 'v8',
      reporter: ['text', 'lcov'],
      exclude: ['**/*.config.*', '**/types/**'],
    },
  },
  resolve: {
    alias: { '@': resolve(__dirname, './src') },
  },
})

// src/test/setup.ts
import '@testing-library/jest-dom'
import { server } from './mocks/server'

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }))
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

### MSW (Mock Service Worker) for API Mocking

```typescript
// src/test/mocks/handlers.ts
import { http, HttpResponse } from 'msw'

export const handlers = [
  http.get('/api/users/:id', ({ params }) => {
    return HttpResponse.json({
      id: params.id,
      name: 'Test User',
      email: 'test@example.com',
    })
  }),

  http.post('/api/posts', async ({ request }) => {
    const body = await request.json()
    return HttpResponse.json({ id: 'post_123', ...body }, { status: 201 })
  }),
]

// src/test/mocks/server.ts
import { setupServer } from 'msw/node'
import { handlers } from './handlers'
export const server = setupServer(...handlers)
```

### Component Testing

```typescript
import { render, screen, userEvent } from '@testing-library/react'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { CreatePostForm } from './CreatePostForm'
import { server } from '@/test/mocks/server'
import { http, HttpResponse } from 'msw'

function renderWithProviders(ui: React.ReactElement) {
  const queryClient = new QueryClient({
    defaultOptions: { queries: { retry: false } }
  })
  return render(
    <QueryClientProvider client={queryClient}>{ui}</QueryClientProvider>
  )
}

describe('CreatePostForm', () => {
  it('submits form and shows success state', async () => {
    const user = userEvent.setup()
    renderWithProviders(<CreatePostForm />)

    await user.type(screen.getByLabelText('Title'), 'My Test Post')
    await user.type(screen.getByLabelText('Content'), 'This is the post content with enough chars.')
    await user.click(screen.getByRole('button', { name: /create/i }))

    expect(await screen.findByText('Post created successfully!')).toBeInTheDocument()
  })

  it('shows validation errors for empty fields', async () => {
    const user = userEvent.setup()
    renderWithProviders(<CreatePostForm />)
    
    await user.click(screen.getByRole('button', { name: /create/i }))
    
    expect(await screen.findByText('Title is required')).toBeInTheDocument()
  })
})
```

### Playwright E2E Testing

```typescript
// tests/auth.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Authentication', () => {
  test('user can sign up and access dashboard', async ({ page }) => {
    await page.goto('/signup')
    
    await page.fill('[name="email"]', 'test@example.com')
    await page.fill('[name="password"]', 'SecurePass123!')
    await page.fill('[name="confirmPassword"]', 'SecurePass123!')
    await page.click('button[type="submit"]')
    
    await expect(page).toHaveURL('/dashboard')
    await expect(page.getByText('Welcome, test@example.com')).toBeVisible()
  })

  test('redirects unauthenticated users', async ({ page }) => {
    await page.goto('/dashboard')
    await expect(page).toHaveURL('/login?redirect=/dashboard')
  })
})

// playwright.config.ts
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './tests',
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'Mobile Chrome', use: { ...devices['Pixel 5'] } },
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
})
```

---

## Performance Optimization

### Core Web Vitals (2025 State)

Google's Core Web Vitals as of 2024/2025:
- **LCP (Largest Contentful Paint)**: ≤2.5s good, ≤4.0s needs improvement
- **INP (Interaction to Next Paint)**: ≤200ms good — **replaced FID in March 2024**
- **CLS (Cumulative Layout Shift)**: ≤0.1 good

INP measures the full interaction latency (input → visual update), making it more comprehensive than FID which only measured input delay.

### LCP Optimization

```tsx
// Preload critical images
// app/layout.tsx
export default function RootLayout() {
  return (
    <html>
      <head>
        <link
          rel="preload"
          href="/hero-image.webp"
          as="image"
          type="image/webp"
        />
      </head>
      <body>...</body>
    </html>
  )
}

// Priority images
import Image from 'next/image'

// Hero image - always priority
<Image
  src="/hero.webp"
  alt="Hero"
  width={1200}
  height={600}
  priority  // preloads, disables lazy loading
  sizes="100vw"
/>

// Below-fold images - lazy loaded (default)
<Image
  src="/product.webp"
  alt="Product"
  width={400}
  height={300}
  sizes="(max-width: 768px) 100vw, 400px"
/>
```

### INP Optimization

```typescript
// Move expensive work off the main thread
// Bad - blocks interaction
function handleClick() {
  const result = heavyComputation(data) // blocks for 500ms
  setResult(result)
}

// Good - yield to browser between tasks
function handleClick() {
  setLoading(true)
  setTimeout(() => {  // yields to browser
    const result = heavyComputation(data)
    setResult(result)
    setLoading(false)
  }, 0)
}

// Better - use startTransition for non-urgent updates
import { startTransition } from 'react'

function handleClick() {
  startTransition(() => {
    setHeavyState(newValue)  // marked as non-urgent, won't block input
  })
}

// Best - Web Workers for CPU-intensive work
const worker = new Worker(new URL('./heavy-worker.ts', import.meta.url))
worker.postMessage({ data })
worker.onmessage = (e) => setResult(e.data.result)
```

### Bundle Analysis

```bash
# next-bundle-analyzer
ANALYZE=true next build

# Dynamic imports for code splitting
const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false,  // client-only (e.g., D3 charts)
})

# Tree shaking - avoid barrel imports
# Bad:
import { Button, Input, Modal } from '@/components'
# Good:
import { Button } from '@/components/Button'
import { Input } from '@/components/Input'
```

---

## Security (OWASP Top 10)

### A1: Broken Access Control

```typescript
// Row-level security in queries - always filter by authenticated user
async function getPost(postId: string, userId: string) {
  const post = await db.query.posts.findFirst({
    where: and(
      eq(posts.id, postId),
      eq(posts.authorId, userId)  // ownership check in query
    ),
  })
  if (!post) throw new TRPCError({ code: 'NOT_FOUND' })
  return post
}

// Never trust client-provided user IDs
// Bad:
app.delete('/posts/:id', (req) => {
  db.deletePost(req.params.id, req.body.userId)  // IDOR vulnerability
})
// Good:
app.delete('/posts/:id', authenticate, (req) => {
  db.deletePost(req.params.id, req.user.id)  // from verified JWT
})
```

### A3: Injection

```typescript
// SQL Injection: use parameterized queries (Drizzle does this automatically)
// Bad (raw SQL):
const users = await db.execute(`SELECT * FROM users WHERE name = '${name}'`)

// Good (Drizzle parameterized):
const users = await db.query.users.findMany({ where: eq(users.name, name) })

// Good (raw with parameters):
const users = await db.execute(sql`SELECT * FROM users WHERE name = ${name}`)

// XSS: React escapes by default, avoid dangerouslySetInnerHTML
// Only use for trusted, sanitized HTML:
import DOMPurify from 'dompurify'
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(htmlContent) }} />
```

### A7: Identification and Authentication Failures

```typescript
// Password hashing
import { hash, verify } from '@node-rs/argon2'

const passwordHash = await hash(password, {
  memoryCost: 19456,  // 19MB
  timeCost: 2,
  outputLen: 32,
  parallelism: 1,
})

const valid = await verify(passwordHash, inputPassword)

// Secure session management
// - httpOnly cookies (no JS access)
// - Secure flag (HTTPS only)
// - SameSite=Lax (CSRF protection)
// - Short-lived tokens with rotation
```

### Content Security Policy

```typescript
// middleware.ts
import { NextResponse, NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  const nonce = Buffer.from(crypto.randomUUID()).toString('base64')
  
  const cspHeader = [
    `default-src 'self'`,
    `script-src 'self' 'nonce-${nonce}' 'strict-dynamic'`,
    `style-src 'self' 'unsafe-inline'`,  // needed for Tailwind
    `img-src 'self' blob: data: https:`,
    `font-src 'self'`,
    `object-src 'none'`,
    `base-uri 'self'`,
    `form-action 'self'`,
    `frame-ancestors 'none'`,
    `upgrade-insecure-requests`,
  ].join('; ')

  const response = NextResponse.next()
  response.headers.set('Content-Security-Policy', cspHeader)
  response.headers.set('X-Frame-Options', 'DENY')
  response.headers.set('X-Content-Type-Options', 'nosniff')
  response.headers.set('Referrer-Policy', 'strict-origin-when-cross-origin')
  response.headers.set('Permissions-Policy', 'camera=(), microphone=(), geolocation=()')
  
  return response
}
```

### Rate Limiting with Upstash

```typescript
import { Ratelimit } from '@upstash/ratelimit'
import { Redis } from '@upstash/redis'

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s'),
  analytics: true,
})

// In API route
export async function POST(req: Request) {
  const ip = req.headers.get('x-forwarded-for') ?? 'anonymous'
  const { success, limit, remaining, reset } = await ratelimit.limit(ip)
  
  if (!success) {
    return new Response('Too Many Requests', {
      status: 429,
      headers: {
        'X-RateLimit-Limit': limit.toString(),
        'X-RateLimit-Remaining': remaining.toString(),
        'X-RateLimit-Reset': new Date(reset).toISOString(),
        'Retry-After': Math.ceil((reset - Date.now()) / 1000).toString(),
      },
    })
  }

  // handle request
}
```

### Input Validation

```typescript
import { z } from 'zod'

// Server-side validation (never trust client)
const CreateUserSchema = z.object({
  email: z.string().email().max(254).toLowerCase(),
  password: z.string().min(8).max(128),
  name: z.string().min(1).max(100).trim(),
  role: z.enum(['user', 'admin']).default('user'),
  // Prevent mass assignment: only allow expected fields
})

// Sanitize HTML
import { JSDOM } from 'jsdom'
import createDOMPurify from 'dompurify'
const window = new JSDOM('').window
const DOMPurify = createDOMPurify(window as any)

function sanitizeHTML(dirty: string): string {
  return DOMPurify.sanitize(dirty, {
    ALLOWED_TAGS: ['p', 'b', 'i', 'em', 'strong', 'a', 'ul', 'ol', 'li'],
    ALLOWED_ATTR: ['href', 'target'],
  })
}
```

---

## Deployment

### Vercel (Frontend + Serverless)

```typescript
// vercel.json
{
  "functions": {
    "app/api/**/*": {
      "maxDuration": 30
    },
    "app/api/ai/**/*": {
      "maxDuration": 300  // AI endpoints need longer timeout
    }
  },
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-Content-Type-Options", "value": "nosniff" }
      ]
    }
  ]
}

// Environment variable management
// .env.local (local dev, never commit)
DATABASE_URL=postgresql://...
NEXTAUTH_SECRET=...

// Vercel dashboard → Settings → Environment Variables
// Production, Preview, Development scopes
```

### Railway / Fly.io (Dedicated Backend)

```dockerfile
# Dockerfile (multi-stage for minimal production image)
FROM node:22-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:22-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:22-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=deps /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json .

RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
USER nodejs

EXPOSE 3000
CMD ["node", "dist/server.js"]
```

### Cloudflare Workers

```typescript
// worker.ts (Hono on Workers)
import { Hono } from 'hono'
import { handle } from 'hono/cloudflare-workers'

const app = new Hono<{
  Bindings: {
    DB: D1Database
    KV: KVNamespace
    R2: R2Bucket
  }
}>()

app.get('/api/config', async (c) => {
  const config = await c.env.KV.get('app-config', 'json')
  return c.json(config)
})

app.post('/api/upload', async (c) => {
  const formData = await c.req.formData()
  const file = formData.get('file') as File
  await c.env.R2.put(`uploads/${crypto.randomUUID()}`, file.stream())
  return c.json({ success: true })
})

export default handle(app)
```

---

## Error Handling and Observability

### Error Boundaries

```tsx
'use client'
import { Component, ReactNode } from 'react'

interface Props {
  fallback: ReactNode | ((error: Error) => ReactNode)
  children: ReactNode
}

interface State {
  error: Error | null
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props)
    this.state = { error: null }
  }

  static getDerivedStateFromError(error: Error): State {
    return { error }
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    // Send to error tracking (Sentry, etc.)
    reportError(error, { componentStack: info.componentStack })
  }

  render() {
    if (this.state.error) {
      const fallback = this.props.fallback
      return typeof fallback === 'function' ? fallback(this.state.error) : fallback
    }
    return this.props.children
  }
}

// Next.js error.tsx (app router)
'use client'
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  useEffect(() => {
    reportError(error)
  }, [error])

  return (
    <div>
      <h2>Something went wrong</h2>
      <button onClick={reset}>Try again</button>
    </div>
  )
}
```

### Structured Logging

```typescript
import pino from 'pino'

export const logger = pino({
  level: process.env.LOG_LEVEL ?? 'info',
  transport: process.env.NODE_ENV === 'development'
    ? { target: 'pino-pretty', options: { colorize: true } }
    : undefined,
  base: {
    env: process.env.NODE_ENV,
    version: process.env.npm_package_version,
  },
  redact: ['req.headers.authorization', 'body.password', 'body.token'],
})

// Usage
logger.info({ userId, action: 'post.create', postId }, 'Post created')
logger.error({ error, userId, requestId }, 'Failed to process payment')
```

---

## Environment and Configuration

```typescript
// lib/env.ts - validated environment variables
import { createEnv } from '@t3-oss/env-nextjs'
import { z } from 'zod'

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    BETTER_AUTH_SECRET: z.string().min(32),
    OPENAI_API_KEY: z.string().startsWith('sk-'),
    ANTHROPIC_API_KEY: z.string().startsWith('sk-ant-'),
    UPSTASH_REDIS_REST_URL: z.string().url(),
    UPSTASH_REDIS_REST_TOKEN: z.string(),
  },
  client: {
    NEXT_PUBLIC_APP_URL: z.string().url(),
    NEXT_PUBLIC_POSTHOG_KEY: z.string().optional(),
  },
  runtimeEnv: {
    DATABASE_URL: process.env.DATABASE_URL,
    BETTER_AUTH_SECRET: process.env.BETTER_AUTH_SECRET,
    // ... etc
  },
})

// Usage: env.DATABASE_URL - throws on missing required vars at startup
```

---

## Production Checklist

### Pre-Launch Security
- [ ] All user inputs validated with Zod on server side
- [ ] Row-level security enforced in all DB queries
- [ ] Authentication required for all protected routes
- [ ] HTTPS enforced, HTTP redirects to HTTPS
- [ ] Security headers set (CSP, HSTS, X-Frame-Options, etc.)
- [ ] Rate limiting on all public endpoints
- [ ] Secrets in environment variables, never in code
- [ ] Dependencies audited (`npm audit`)
- [ ] Error messages don't expose internal details to clients

### Performance
- [ ] LCP < 2.5s on mobile (test with Lighthouse)
- [ ] INP < 200ms (test with Chrome DevTools)
- [ ] CLS < 0.1 (no layout shifts)
- [ ] Images optimized (WebP/AVIF, proper sizing, lazy loading)
- [ ] Fonts preloaded, font-display: swap
- [ ] Bundle < 200KB initial JS (analyze with `@next/bundle-analyzer`)
- [ ] Database queries have appropriate indexes

### Reliability
- [ ] Error tracking configured (Sentry)
- [ ] Structured logging in production
- [ ] Health check endpoint (`/api/health`)
- [ ] Database connection pooling configured
- [ ] Graceful shutdown handling
- [ ] Retry logic on external API calls
- [ ] Timeouts on all outbound requests

### CI/CD Pipeline
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npm run type-check
      - run: npm run lint
      - run: npm run test:unit
      - run: npm run build

  e2e:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npx playwright install --with-deps chromium
      - run: npm run test:e2e
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

---

## Expert-Level Patterns

### Optimistic UI with Server Actions

```tsx
'use client'
import { useOptimistic, startTransition } from 'react'
import { likePost } from '@/actions/posts'

function PostCard({ post }: { post: Post }) {
  const [optimisticPost, addOptimisticLike] = useOptimistic(
    post,
    (state, liked: boolean) => ({
      ...state,
      liked,
      likeCount: liked ? state.likeCount + 1 : state.likeCount - 1,
    })
  )

  async function handleLike() {
    startTransition(() => {
      addOptimisticLike(!optimisticPost.liked)
    })
    await likePost(post.id)
  }

  return (
    <button onClick={handleLike}>
      {optimisticPost.liked ? '❤️' : '🤍'} {optimisticPost.likeCount}
    </button>
  )
}
```

### Parallel Route with Intercepting Routes (Modals)

```
app/
  @modal/
    (.)photos/[id]/
      page.tsx    ← intercepts /photos/[id] when navigating within app
  photos/
    [id]/
      page.tsx    ← direct URL access shows full page
  layout.tsx      ← renders both {children} and {modal}
```

```tsx
// app/layout.tsx
export default function Layout({
  children,
  modal,
}: {
  children: React.ReactNode
  modal: React.ReactNode
}) {
  return (
    <>
      {children}
      {modal}  {/* renders the intercepted modal */}
    </>
  )
}
```

### Streaming with Suspense Boundaries

```tsx
// Waterfall problem: all three await before anything renders
export default async function Dashboard() {
  const user = await getUser()
  const posts = await getPosts(user.id)  // waits for getUser
  const metrics = await getMetrics(user.id)  // waits for getPosts
  return <div>{...}</div>
}

// Solution: parallel fetch with Suspense streaming
export default async function Dashboard() {
  return (
    <div>
      {/* Critical data - streamed first */}
      <Suspense fallback={<UserSkeleton />}>
        <UserHeader />  {/* async server component */}
      </Suspense>
      
      {/* Non-critical - can stream later */}
      <Suspense fallback={<PostsSkeleton />}>
        <PostsList />
      </Suspense>
      
      <Suspense fallback={<MetricsSkeleton />}>
        <MetricsPanel />
      </Suspense>
    </div>
  )
}

// Each async component fetches independently, in parallel
async function UserHeader() {
  const user = await getUser()  // no waiting on other components
  return <header>{user.name}</header>
}
```

---

## Technology Selection Guide

| Decision | Options | Choose When |
|----------|---------|-------------|
| **ORM** | Drizzle | Edge runtime, SQL expertise, bundle size matters |
| | Prisma 7 | Rapid prototype, complex relations, team familiarity |
| **State** | TanStack Query | Server state, caching, mutations |
| | Zustand | Simple global UI state |
| | Jotai | Granular, derived, async atomic state |
| **Backend** | tRPC | TypeScript-only stack, internal APIs |
| | Hono | Multi-runtime, REST APIs, edge deployment |
| | Fastify | Node.js only, max performance, OpenAPI |
| **Auth** | Better Auth | Full control, open source, complex requirements |
| | Clerk | Fastest integration, managed UI, enterprise |
| | Auth.js v5 | Standard OAuth, open source, framework-native |
| **Deploy** | Vercel | Frontend + serverless, DX priority |
| | Railway | Dedicated servers, databases, simplicity |
| | Fly.io | Multi-region, custom infra, cost optimization |
| | Cloudflare Workers | Global edge, V8 isolates, $0 cold starts |
