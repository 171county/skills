---
name: full-stack-app-expert
description: Expert-level full-stack application architect and engineer covering React 18/19, Next.js 15 App Router, server-side rendering, API design, database architecture, authentication, AI integration, observability, security, testing, CI/CD, and production deployment. Invoke when architecting, building, debugging, or optimizing full-stack web applications across the modern JavaScript/TypeScript ecosystem.
---

You are operating as a world-class full-stack application engineer and architect — combining deep React internals knowledge, Next.js App Router mastery, database and API design expertise, production reliability engineering, and AI integration capability. You understand not just how to write code that works, but how to build systems that scale, remain maintainable, perform reliably under load, and ship new features without fear. Apply this expertise to make the right architectural decisions early, avoid the pitfalls that cause rewrites, and build applications that are production-ready from the start.

## 1. React 18 and 19 Architecture

**React 18 Core Concepts**
- Concurrent rendering: enables React to interrupt, pause, and resume rendering work
- `useTransition`: mark state updates as non-urgent; keep UI responsive during expensive transitions
- `useDeferredValue`: defer re-rendering of expensive child components
- Suspense: declarative loading states; works with async data fetching and lazy loading
- Automatic batching: state updates inside event handlers, setTimeout, Promises all batched automatically
- Streaming SSR: `renderToPipeableStream` enables streaming HTML with progressive hydration

**React 19 Key Additions**
```tsx
// useActionState: replaces useFormState; handles async form actions
import { useActionState } from 'react';

function SubmitButton() {
  const [state, formAction, isPending] = useActionState(
    async (prevState: State, formData: FormData) => {
      const result = await createPost(formData.get('title') as string);
      return result.success ? { success: true } : { error: result.error };
    },
    { success: false }
  );
  
  return (
    <form action={formAction}>
      <input name="title" />
      <button disabled={isPending}>{isPending ? 'Saving...' : 'Save'}</button>
      {state.error && <p>{state.error}</p>}
    </form>
  );
}

// useOptimistic: immediate optimistic UI before async operation completes
import { useOptimistic } from 'react';

function TodoList({ todos }: { todos: Todo[] }) {
  const [optimisticTodos, addOptimistic] = useOptimistic(
    todos,
    (state, newTodo: Todo) => [...state, { ...newTodo, pending: true }]
  );
  
  async function addTodo(formData: FormData) {
    const title = formData.get('title') as string;
    addOptimistic({ id: crypto.randomUUID(), title, completed: false });
    await createTodo(title);
  }
  
  return (
    <form action={addTodo}>
      {optimisticTodos.map(todo => <TodoItem key={todo.id} todo={todo} />)}
    </form>
  );
}

// use(): unwrap promises and contexts in render (not just top-level hooks)
import { use } from 'react';

function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise); // Suspense boundary required above
  return <div>{user.name}</div>;
}
```

**React Compiler (React 19+)**
- Automatic memoization: compiler analyzes code and adds memo/useCallback/useMemo
- Enables removing most manual memoization: `memo()`, `useMemo()`, `useCallback()`
- Opt-in: `babel-plugin-react-compiler` or `eslint-plugin-react-compiler`
- Works with strict rules of React (pure functions, stable references)

**Component Tree Optimal Structure**
```
App
├── Providers (context, query client, theme) — stable, rarely re-render
├── Layout (nav, sidebar, footer) — server component where possible
│   └── ErrorBoundary → Suspense → Page
│       ├── Server Components (data fetching, heavy computation)
│       │   └── Client Components (interactivity, browser APIs)
│       └── Parallel data loading (Promise.all / parallel suspense)
```

**Rules of Server Components**
- Cannot use: useState, useEffect, browser APIs, event handlers, third-party context
- Can use: async/await, direct database access, environment variables (server-only), heavy npm packages
- Pass server data to client components as props (serializable values only)

## 2. Next.js 15 App Router

**Project Structure**
```
app/
├── layout.tsx              # Root layout (persists across navigations)
├── page.tsx                # Home route
├── error.tsx               # Error boundary
├── not-found.tsx           # 404 page
├── loading.tsx             # Suspense fallback
├── (marketing)/            # Route group (no URL segment)
│   ├── about/page.tsx
│   └── pricing/page.tsx
├── dashboard/
│   ├── layout.tsx          # Nested layout
│   ├── page.tsx
│   └── settings/page.tsx
├── api/
│   └── webhooks/
│       └── stripe/route.ts # API route handler
└── _components/            # Private folder (not a route)
    └── button.tsx
```

**Caching Behavior (Next.js 15 — IMPORTANT CHANGE)**
Next.js 15 changed default caching: `fetch()` now defaults to `no-store` (was `force-cache`).

```typescript
// Explicit cache control required in Next.js 15
const data = await fetch('/api/data', { cache: 'force-cache' });    // Cached
const data = await fetch('/api/data', { cache: 'no-store' });       // Never cached
const data = await fetch('/api/data', { next: { revalidate: 60 }}); // ISR: revalidate every 60s
const data = await fetch('/api/data', { next: { tags: ['posts'] }}); // Tag-based revalidation

// On-demand revalidation
import { revalidateTag, revalidatePath } from 'next/cache';
await revalidateTag('posts');       // Invalidate all fetches with tag 'posts'
await revalidatePath('/blog');      // Invalidate specific path
```

**Four Cache Layers**
1. **Request Memoization**: deduplicates identical `fetch` calls within a single render tree
2. **Data Cache**: persists across requests; controlled by `cache` option
3. **Full Route Cache**: cached HTML at build time (static routes only)
4. **Router Cache**: client-side cache of visited route segments; 30s (dynamic) / 5min (static)

**Server Actions**
```typescript
// app/actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { db } from '@/lib/db';
import { auth } from '@/lib/auth';

export async function createPost(formData: FormData) {
  const session = await auth();
  if (!session) throw new Error('Unauthorized');
  
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;
  
  if (!title || title.length < 3) {
    return { error: 'Title must be at least 3 characters' };
  }
  
  const post = await db.post.create({
    data: { title, content, authorId: session.user.id }
  });
  
  revalidatePath('/blog');
  return { success: true, postId: post.id };
}

// Usage in client component
'use client';
import { createPost } from '@/app/actions/posts';
import { useActionState } from 'react';

export function NewPostForm() {
  const [state, action, pending] = useActionState(createPost, null);
  return <form action={action}>...</form>;
}
```

**Route Handlers (API Routes)**
```typescript
// app/api/posts/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }  // params are now async in Next.js 15
) {
  const { id } = await params;
  const post = await db.post.findUnique({ where: { id } });
  
  if (!post) {
    return NextResponse.json({ error: 'Not found' }, { status: 404 });
  }
  
  return NextResponse.json(post);
}

export async function DELETE(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const session = await auth();
  if (!session) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  
  const { id } = await params;
  await db.post.delete({ where: { id, authorId: session.user.id } });
  
  return NextResponse.json({ success: true });
}
```

## 3. Data Fetching and State Management

**TanStack Query (React Query) — Client-Side Data**
```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Query with optimistic updates
function PostList() {
  const queryClient = useQueryClient();
  
  const { data: posts, isLoading } = useQuery({
    queryKey: ['posts'],
    queryFn: async () => {
      const res = await fetch('/api/posts');
      if (!res.ok) throw new Error('Failed to fetch');
      return res.json();
    },
    staleTime: 60 * 1000,          // Consider fresh for 60 seconds
    gcTime: 5 * 60 * 1000,         // Keep in cache for 5 minutes
  });
  
  const deleteMutation = useMutation({
    mutationFn: (id: string) => fetch(`/api/posts/${id}`, { method: 'DELETE' }),
    onMutate: async (id) => {
      await queryClient.cancelQueries({ queryKey: ['posts'] });
      const previous = queryClient.getQueryData<Post[]>(['posts']);
      
      // Optimistic update
      queryClient.setQueryData<Post[]>(['posts'], old => old?.filter(p => p.id !== id));
      
      return { previous };
    },
    onError: (err, id, context) => {
      // Rollback on error
      queryClient.setQueryData(['posts'], context?.previous);
    },
    onSettled: () => queryClient.invalidateQueries({ queryKey: ['posts'] }),
  });
  
  if (isLoading) return <Skeleton />;
  return <ul>{posts?.map(post => <PostItem key={post.id} post={post} />)}</ul>;
}
```

**Zustand — Client State**
```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface UIStore {
  sidebarOpen: boolean;
  selectedView: 'list' | 'grid';
  toggleSidebar: () => void;
  setView: (view: 'list' | 'grid') => void;
}

export const useUIStore = create<UIStore>()(
  persist(
    (set) => ({
      sidebarOpen: true,
      selectedView: 'list',
      toggleSidebar: () => set(state => ({ sidebarOpen: !state.sidebarOpen })),
      setView: (view) => set({ selectedView: view }),
    }),
    { name: 'ui-preferences' }  // Persists to localStorage
  )
);
```

**tRPC — End-to-End Type Safety**
```typescript
// server/trpc/router.ts
import { router, publicProcedure, protectedProcedure } from './trpc';
import { z } from 'zod';

export const postRouter = router({
  list: publicProcedure
    .input(z.object({ limit: z.number().min(1).max(100).default(10) }))
    .query(async ({ input }) => {
      return db.post.findMany({ take: input.limit, orderBy: { createdAt: 'desc' } });
    }),
    
  create: protectedProcedure
    .input(z.object({
      title: z.string().min(3).max(200),
      content: z.string().min(10),
    }))
    .mutation(async ({ ctx, input }) => {
      return db.post.create({
        data: { ...input, authorId: ctx.session.user.id }
      });
    }),
});

// Client usage — fully typed, no codegen needed
const { data } = trpc.post.list.useQuery({ limit: 20 });
const createPost = trpc.post.create.useMutation();
await createPost.mutateAsync({ title: 'Hello', content: 'World...' });
```

## 4. Database and ORM Layer

**Drizzle ORM — Type-Safe SQL**
```typescript
// schema.ts
import { pgTable, text, timestamp, uuid, boolean } from 'drizzle-orm/pg-core';
import { relations } from 'drizzle-orm';

export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: text('email').notNull().unique(),
  name: text('name').notNull(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
});

export const posts = pgTable('posts', {
  id: uuid('id').primaryKey().defaultRandom(),
  title: text('title').notNull(),
  content: text('content').notNull(),
  published: boolean('published').default(false).notNull(),
  authorId: uuid('author_id').references(() => users.id, { onDelete: 'cascade' }),
  createdAt: timestamp('created_at').defaultNow().notNull(),
});

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}));

// db.ts
import { drizzle } from 'drizzle-orm/postgres-js';
import postgres from 'postgres';

const client = postgres(process.env.DATABASE_URL!);
export const db = drizzle(client, { schema: { users, posts, usersRelations } });

// Usage — fully typed
const userPosts = await db.query.users.findFirst({
  where: eq(users.id, userId),
  with: { posts: { where: eq(posts.published, true) } }
});
```

**Database Selection Guide**

| Database | Best For | Hosted Options |
|----------|---------|----------------|
| PostgreSQL | General purpose, complex queries, JSON, full-text search | Neon, Supabase, Railway, RDS |
| MySQL | Read-heavy workloads, legacy compatibility | PlanetScale, RDS |
| SQLite | Embedded, single-server, <100k users | Turso, Cloudflare D1, local dev |
| MongoDB | Document-centric, flexible schema | Atlas |
| Redis | Caching, sessions, pub/sub, queues | Upstash, Redis Cloud |
| Qdrant/Pinecone | Vector search, AI embeddings | Managed cloud |

**Connection Pooling**
```typescript
// Singleton pattern for Next.js (prevents new connections on hot-reload)
import { drizzle } from 'drizzle-orm/postgres-js';
import postgres from 'postgres';

declare global {
  var db: ReturnType<typeof drizzle> | undefined;
}

const client = postgres(process.env.DATABASE_URL!, {
  max: 10,          // Max connections
  idle_timeout: 30, // Seconds before idle connection closed
});

export const db = globalThis.db ?? drizzle(client);
if (process.env.NODE_ENV !== 'production') globalThis.db = db;
```

## 5. Authentication

**Auth.js v5 (Next-Auth)**
```typescript
// auth.ts
import NextAuth from 'next-auth';
import GitHub from 'next-auth/providers/github';
import { DrizzleAdapter } from '@auth/drizzle-adapter';
import { db } from '@/lib/db';

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: DrizzleAdapter(db),
  providers: [
    GitHub({ clientId: process.env.GITHUB_ID!, clientSecret: process.env.GITHUB_SECRET! }),
  ],
  callbacks: {
    session({ session, user }) {
      session.user.id = user.id;
      return session;
    },
  },
});

// middleware.ts — protect routes
export { auth as middleware } from '@/auth';
export const config = { matcher: ['/dashboard/:path*', '/api/protected/:path*'] };

// Usage in Server Component
const session = await auth();
if (!session) redirect('/login');
```

**Clerk (Managed Auth Service)**
```typescript
// Much simpler for full auth solution
import { auth, currentUser } from '@clerk/nextjs/server';

export async function GET() {
  const { userId } = await auth();
  if (!userId) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  
  const user = await currentUser();
  return NextResponse.json({ name: user?.firstName });
}
```

## 6. UI Component Architecture

**shadcn/ui — Component Code Generator**
shadcn/ui is NOT an npm package — it's a CLI that copies component source code into your project:
```bash
npx shadcn@latest init      # Initialize (creates components.json)
npx shadcn@latest add button card dialog form input table toast
```
Components live in `components/ui/` — fully customizable, you own the code.

**Tailwind CSS v4 (CSS-First Configuration)**
```css
/* app/globals.css — configuration moved to CSS */
@import "tailwindcss";

@theme {
  --color-primary: oklch(0.6 0.2 270);
  --color-secondary: oklch(0.7 0.15 180);
  --font-sans: 'Inter', system-ui, sans-serif;
  --radius-lg: 0.75rem;
  --spacing-page: 1.5rem;
}

/* No tailwind.config.ts required in v4 */
/* ~10x faster build times vs v3 */
```

**Component Patterns**
```tsx
// Compound component pattern for complex UI
export function DataTable({ children, data }: DataTableProps) {
  return <TableProvider data={data}>{children}</TableProvider>;
}
DataTable.Header = DataTableHeader;
DataTable.Body = DataTableBody;
DataTable.Pagination = DataTablePagination;

// Usage
<DataTable data={posts}>
  <DataTable.Header />
  <DataTable.Body />
  <DataTable.Pagination />
</DataTable>
```

## 7. API Design

**Hono — Edge-First API Framework**
```typescript
import { Hono } from 'hono';
import { zValidator } from '@hono/zod-validator';
import { z } from 'zod';

const app = new Hono();

// Type-safe RPC-style routing
const routes = app
  .get('/posts', async (c) => {
    const posts = await db.post.findMany();
    return c.json(posts);
  })
  .post('/posts', 
    zValidator('json', z.object({ title: z.string().min(3), content: z.string() })),
    async (c) => {
      const body = c.req.valid('json');
      const post = await db.post.create({ data: body });
      return c.json(post, 201);
    }
  )
  .delete('/posts/:id', async (c) => {
    const id = c.req.param('id');
    await db.post.delete({ where: { id } });
    return c.json({ success: true });
  });

// Export types for client-side type inference
export type AppType = typeof routes;

// Hono RPC Client (full type safety, no codegen)
import { hc } from 'hono/client';
const client = hc<AppType>('https://api.example.com');
const posts = await client.posts.$get();
```

**REST API Design Standards**
```
GET    /api/posts          → list posts (with ?page=1&limit=20&sort=-createdAt)
POST   /api/posts          → create post
GET    /api/posts/:id      → get single post
PATCH  /api/posts/:id      → partial update
DELETE /api/posts/:id      → delete post
POST   /api/posts/:id/publish → state transition (use verbs for actions, nouns for resources)
```

**API Response Shape**
```typescript
// Consistent envelope
{
  "data": [...],
  "meta": { "total": 100, "page": 1, "perPage": 20 },
  "error": null
}

// Error response
{
  "data": null,
  "error": { "code": "VALIDATION_ERROR", "message": "...", "details": [...] }
}
```

## 8. AI Integration

**Vercel AI SDK — Streaming AI Responses**
```typescript
// app/api/chat/route.ts
import { streamText } from 'ai';
import { anthropic } from '@ai-sdk/anthropic';

export const runtime = 'edge';

export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = streamText({
    model: anthropic('claude-opus-4-8'),
    system: 'You are a helpful assistant.',
    messages,
    maxTokens: 2048,
  });
  
  return result.toDataStreamResponse();
}

// Client component
'use client';
import { useChat } from 'ai/react';

export function ChatInterface() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: '/api/chat',
  });
  
  return (
    <div>
      {messages.map(m => (
        <div key={m.id} className={m.role === 'user' ? 'text-right' : 'text-left'}>
          {m.content}
        </div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} disabled={isLoading} />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  );
}
```

**RAG Embeddings Pipeline**
```typescript
import { embed, cosineSimilarity } from 'ai';
import { openai } from '@ai-sdk/openai';

// Generate embedding
async function embedText(text: string): Promise<number[]> {
  const { embedding } = await embed({
    model: openai.embedding('text-embedding-3-small'),
    value: text,
  });
  return embedding;
}

// Store in vector database (example: Qdrant)
async function indexDocument(content: string, metadata: object) {
  const embedding = await embedText(content);
  await qdrant.upsert('documents', {
    points: [{ id: crypto.randomUUID(), vector: embedding, payload: { content, ...metadata } }]
  });
}

// Retrieve relevant documents
async function search(query: string, topK = 5) {
  const queryEmbedding = await embedText(query);
  const results = await qdrant.search('documents', {
    vector: queryEmbedding,
    limit: topK,
    score_threshold: 0.7,
  });
  return results.map(r => r.payload.content);
}
```

## 9. Testing Architecture

**Testing Pyramid**
```
          ┌─────────────────┐
          │  E2E (Playwright)│  <- Slowest; test critical user flows
          │   5-10% of tests │
        ┌─┴─────────────────┴─┐
        │  Integration Tests   │  <- API routes, DB operations
        │  20-30% of tests     │
      ┌─┴──────────────────────┴─┐
      │     Unit Tests (Vitest)   │  <- Business logic, utilities
      │     60-70% of tests       │
      └───────────────────────────┘
```

**Vitest — Unit and Integration**
```typescript
// __tests__/posts.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { createPost } from '@/lib/posts';
import { db } from '@/lib/db';

vi.mock('@/lib/db');

describe('createPost', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });
  
  it('creates a post with valid inputs', async () => {
    const mockPost = { id: '1', title: 'Test', content: 'Content', authorId: 'user-1' };
    vi.mocked(db.post.create).mockResolvedValue(mockPost);
    
    const result = await createPost({ title: 'Test', content: 'Content', authorId: 'user-1' });
    
    expect(result).toEqual(mockPost);
    expect(db.post.create).toHaveBeenCalledWith({
      data: { title: 'Test', content: 'Content', authorId: 'user-1' }
    });
  });
  
  it('rejects titles shorter than 3 characters', async () => {
    await expect(createPost({ title: 'Hi', content: 'Content', authorId: 'user-1' }))
      .rejects.toThrow('Title must be at least 3 characters');
  });
});
```

**Playwright — E2E Testing**
```typescript
// e2e/auth.spec.ts
import { test, expect } from '@playwright/test';

test.describe('authentication', () => {
  test('user can sign in with GitHub', async ({ page }) => {
    await page.goto('/login');
    await page.getByRole('button', { name: 'Sign in with GitHub' }).click();
    
    // Mock OAuth in test environment
    await expect(page).toHaveURL('/dashboard');
    await expect(page.getByRole('heading', { name: 'Welcome' })).toBeVisible();
  });
  
  test('protected routes redirect unauthenticated users', async ({ page }) => {
    await page.goto('/dashboard');
    await expect(page).toHaveURL('/login');
  });
});
```

**MSW — API Mocking**
```typescript
// src/mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/posts', () => {
    return HttpResponse.json([
      { id: '1', title: 'Test Post', content: 'Test content' }
    ]);
  }),
  
  http.post('/api/posts', async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({ id: '2', ...body }, { status: 201 });
  }),
];
```

## 10. CI/CD and Deployment

**GitHub Actions Pipeline**
```yaml
# .github/workflows/ci.yml
name: CI/CD

on:
  push:
    branches: [main, dev]
  pull_request:
    branches: [main]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci
      - run: npm run type-check
      - run: npm run lint
      - run: npm run test
      - run: npm run build

  e2e:
    needs: check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npm run test:e2e
        env:
          DATABASE_URL: ${{ secrets.TEST_DATABASE_URL }}

  deploy:
    needs: [check, e2e]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npx vercel --prod --token ${{ secrets.VERCEL_TOKEN }}
```

**Multi-Stage Docker Build**
```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

RUN addgroup --system nodejs && adduser --system nextjs
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs
EXPOSE 3000
CMD ["node", "server.js"]
```

**Deployment Platform Comparison**

| Platform | Best For | Cold Starts | Pricing |
|----------|---------|-------------|---------|
| Vercel | Next.js (first-class), SSR | Minimal | Per-request (generous free tier) |
| Railway | Full-stack, Docker, databases | None (persistent) | Usage-based, ~$5/mo base |
| Fly.io | Global edge, Docker, WebSockets | None | Usage-based, free tier |
| Cloudflare Workers | Edge compute, globally distributed | None (instant) | Per-request, very cheap |
| AWS App Runner | Enterprise, compliance, VPC | Minimal | $0.064/vCPU-hr |

## 11. Performance and Core Web Vitals

**Core Web Vitals Targets (2025)**

| Metric | Good | Needs Work | Poor |
|--------|------|------------|------|
| LCP (Largest Contentful Paint) | <2.5s | 2.5-4s | >4s |
| INP (Interaction to Next Paint) | <200ms | 200-500ms | >500ms |
| CLS (Cumulative Layout Shift) | <0.1 | 0.1-0.25 | >0.25 |

**Performance Optimization Checklist**
```typescript
// Image optimization — always use Next.js Image
import Image from 'next/image';
<Image src="/hero.jpg" alt="Hero" width={1200} height={600} priority />  // LCP image: priority

// Font optimization — avoid FOIT/FOUT
import { Inter } from 'next/font/google';
const inter = Inter({ subsets: ['latin'], display: 'swap' });

// Bundle analysis
// next.config.ts
import bundleAnalyzer from '@next/bundle-analyzer';
const withBundleAnalyzer = bundleAnalyzer({ enabled: process.env.ANALYZE === 'true' });
export default withBundleAnalyzer({});

// Lazy load heavy components
const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false,  // Skip SSR for browser-only libs
});
```

## 12. Security — OWASP Top 10 (2025)

**Critical Security Patterns**
```typescript
// 1. SQL Injection — use ORM parameterized queries ONLY
// NEVER: db.$queryRaw`SELECT * FROM users WHERE email = '${email}'`
// CORRECT: db.user.findUnique({ where: { email } })

// 2. XSS — Next.js escapes JSX by default; dangerous patterns:
// NEVER: <div dangerouslySetInnerHTML={{ __html: userContent }} />
// If needed: sanitize with DOMPurify first

// 3. CSRF — Next.js Server Actions include built-in CSRF protection
// For custom API routes: use SameSite cookies + Origin header validation

// 4. Authentication bypass — always verify session server-side
export async function protectedAction() {
  'use server';
  const session = await auth();
  if (!session?.user?.id) throw new Error('Unauthorized');  // Never trust client-side session
}

// 5. Mass assignment — explicit field selection
const user = await db.user.update({
  where: { id },
  data: {
    name: body.name,      // Explicit: only update allowed fields
    bio: body.bio,
    // NEVER: ...body (would allow updating email, role, etc.)
  },
  select: { id: true, name: true, bio: true }  // Never return password, tokens
});

// 6. Environment variable exposure — prefix check
// NEXT_PUBLIC_* vars are bundled to client — never put secrets here
// Server-only: process.env.DATABASE_URL (no NEXT_PUBLIC_ prefix)
```

**Security Headers**
```typescript
// next.config.ts
const securityHeaders = [
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'X-Frame-Options', value: 'DENY' },
  { key: 'X-XSS-Protection', value: '1; mode=block' },
  { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
  { key: 'Permissions-Policy', value: 'camera=(), microphone=(), geolocation=()' },
  { key: 'Content-Security-Policy', value: "default-src 'self'; script-src 'self' 'unsafe-eval'" },
];
```

## 13. Observability

**Production Observability Stack**
```typescript
// Sentry — error tracking
import * as Sentry from '@sentry/nextjs';
Sentry.captureException(error, { extra: { userId, action: 'createPost' } });

// OpenTelemetry — distributed tracing
import { trace, SpanStatusCode } from '@opentelemetry/api';
const tracer = trace.getTracer('my-service');

const span = tracer.startSpan('database.query');
try {
  const result = await db.post.findMany();
  span.setStatus({ code: SpanStatusCode.OK });
  return result;
} catch (err) {
  span.recordException(err);
  span.setStatus({ code: SpanStatusCode.ERROR });
  throw err;
} finally {
  span.end();
}

// Structured logging (Axiom / Better Stack)
import log from '@/lib/logger';
log.info({ event: 'post.created', userId, postId, duration: Date.now() - start });
```

## 14. Monorepo Architecture

**Turborepo Structure**
```
apps/
├── web/          # Next.js app
├── mobile/       # Expo React Native
└── docs/         # Documentation site
packages/
├── ui/           # Shared shadcn/ui components
├── database/     # Drizzle schema + client
├── auth/         # Auth.js config
├── config/       # ESLint, TypeScript, Tailwind configs
└── utils/        # Shared utilities
turbo.json        # Pipeline configuration
```

```json
// turbo.json
{
  "pipeline": {
    "build": { "dependsOn": ["^build"], "outputs": [".next/**", "dist/**"] },
    "dev": { "cache": false, "persistent": true },
    "test": { "dependsOn": ["^build"] },
    "lint": {}
  }
}
```

## 15. Expert Mental Models and Anti-Patterns

**The "Server Component First" Rule**
Default to Server Components for every new component. Add `'use client'` only when you need interactivity, browser APIs, or React hooks. The wrong default (client-first) eliminates SSR benefits, increases bundle size, and creates data-fetching waterfalls. The right default (server-first) is almost always faster, cheaper, and simpler.

**The "Waterfall Killer" Pattern**
Sequential awaits in Server Components create waterfalls:
```typescript
// WRONG: sequential — 3 seconds if each query takes 1 second
const user = await getUser(id);
const posts = await getPosts(user.id);
const settings = await getSettings(user.id);

// RIGHT: parallel — 1 second total
const [user, posts, settings] = await Promise.all([
  getUser(id),
  getPosts(id),
  getSettings(id),
]);
```

**The "Cache Invalidation Boundary" Problem**
In Next.js 15, cached data at different layers has different invalidation mechanisms. Know which cache layer holds your data and the right invalidation: `revalidateTag` for data cache, `router.refresh()` for router cache, `revalidatePath` for full route cache. Mixing these up creates stale UI that looks like bugs.

**The "Optimistic UI Trap"**
Optimistic updates feel great until they fail silently. Always: (1) apply optimistic state, (2) handle rollback in onError, (3) ensure final state always syncs from server in onSettled. The optimistic state is a temporary illusion — server state is ground truth.

**The "N+1 ORM Problem"**
Every ORM creates N+1 risks: fetching a list then fetching related data per item:
```typescript
// WRONG: 1 query for users + N queries for posts
const users = await db.user.findMany();
for (const user of users) {
  user.posts = await db.post.findMany({ where: { authorId: user.id } }); // N queries
}

// RIGHT: 1 query with include
const users = await db.user.findMany({ include: { posts: true } }); // JOIN
```

**The "Environment Variable Leakage" Anti-Pattern**
`NEXT_PUBLIC_` prefix variables are bundled into the client JavaScript and visible to anyone who opens DevTools. Never prefix database credentials, API keys, or secrets with `NEXT_PUBLIC_`. Server-side secrets must remain server-only (no `NEXT_PUBLIC_` prefix, access only in Server Components, Route Handlers, and Server Actions).

**The "Hydration Mismatch" Root Cause**
Hydration mismatches occur when server HTML doesn't match what React renders on client. Common causes: `new Date()`, `Math.random()`, browser-only APIs, user agent detection, locale differences. Solution: use `useEffect` for browser-only rendering, pass stable server-generated values as props, or use `suppressHydrationWarning` for intentional mismatches (timestamps).

**The "Type Safety All the Way Down" Principle**
The most valuable property of the modern TypeScript stack is end-to-end type safety: Drizzle schema → tRPC router → React component. When this chain breaks (any `any`, unvalidated API response, non-typed form data), bugs migrate to runtime. Maintain the chain: Zod for validation, tRPC or typed fetch for API, Drizzle for DB — no gaps.

**The "Bundle Size First" Mindset**
Every `npm install` is a potential user experience tax. Before adding a dependency: check bundle size on bundlephobia.com, check if functionality exists in platform APIs or existing dependencies, prefer tree-shakeable packages, use dynamic imports for large libraries. A 500KB bundle on slow 3G = 5+ second load = 30%+ bounce rate increase.
