---
name: full-stack-app
description: Expert-level skill covering modern full-stack application development. Covers React 19, Next.js 15, TypeScript, Drizzle ORM, TanStack Query, Tailwind CSS v4, shadcn/ui, Vercel AI SDK, authentication (Auth.js / Clerk), testing, and deployment. Includes security best practices, performance patterns, and the T3 stack. Use this when building, reviewing, debugging, or architecting modern full-stack web applications.
---

# Full Stack App Expert

## The 2025–2026 Full-Stack Landscape

The canonical expert-level stack as of mid-2026:

```
Frontend:          React 19 + TypeScript 5.5
Framework:         Next.js 15 (App Router + PPR)
Styling:           Tailwind CSS v4 + shadcn/ui
State (server):    TanStack Query v5 (React Query)
State (client):    Zustand v5 / Jotai
ORM:               Drizzle ORM (preferred) or Prisma 7
Database:          PostgreSQL (Neon serverless / PlanetScale / Supabase)
Auth:              Auth.js v5 (NextAuth) or Clerk
AI integration:    Vercel AI SDK 4.x
Testing:           Vitest + Testing Library + Playwright
Linting/format:    Biome (replaces ESLint + Prettier in many setups)
Deployment:        Vercel (Next.js) / Cloudflare Workers (edge)
```

---

## React 19: What Changed and Why It Matters

### Actions API (The Biggest Shift)

React 19 introduces **Actions** as a first-class pattern for server mutations. The old pattern (useState + fetch + optimistic update) is replaced:

```tsx
// OLD: useState + fetch + error handling + optimistic update (lots of code)
// NEW: useActionState (React 19)

"use client";
import { useActionState } from "react";

async function submitAction(prevState: State, formData: FormData) {
  // This runs on the server (with use server directive) or client
  const name = formData.get("name") as string;
  
  if (!name) return { error: "Name is required", success: false };
  
  await db.users.create({ name });
  return { error: null, success: true };
}

function MyForm() {
  const [state, formAction, isPending] = useActionState(submitAction, {
    error: null,
    success: false,
  });

  return (
    <form action={formAction}>
      <input name="name" />
      {state.error && <p className="text-red-500">{state.error}</p>}
      <button type="submit" disabled={isPending}>
        {isPending ? "Saving..." : "Save"}
      </button>
    </form>
  );
}
```

**useOptimistic** — optimistic UI without libraries:
```tsx
const [optimisticItems, addOptimisticItem] = useOptimistic(
  items,
  (state, newItem) => [...state, { ...newItem, pending: true }]
);

async function addItem(formData: FormData) {
  const item = { id: crypto.randomUUID(), name: formData.get("name") };
  addOptimisticItem(item);  // immediately shows in UI
  await createItemInDB(item);  // server catches up
}
```

### React Compiler (Production Ready 2025)

The React Compiler (formerly React Forget) automatically memoizes components, eliminating the need for manual `useMemo`, `useCallback`, and `React.memo` in most cases.

**Setup:**
```bash
npm install babel-plugin-react-compiler
```
```js
// babel.config.js
module.exports = {
  plugins: ["babel-plugin-react-compiler"],
};
```

**What it does:** Analyzes your component code statically, inserts optimal memoization. Components that were previously over-rendering due to missing `useCallback` now render correctly automatically.

**What it doesn't do:** Fix genuinely expensive computations or broken dependency arrays where the logic is wrong.

### use() Hook for Async Resources

```tsx
import { use, Suspense } from "react";

function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);  // suspends until resolved
  return <div>{user.name}</div>;
}

// Usage:
<Suspense fallback={<Skeleton />}>
  <UserProfile userPromise={fetchUser(id)} />
</Suspense>
```

**use() can be called conditionally** (unlike hooks) — valid in if/else blocks.

---

## Next.js 15: App Router Patterns

### Partial Prerendering (PPR)

The signature feature of Next.js 15. Combines static and dynamic rendering in a single route.

```tsx
// next.config.ts
import type { NextConfig } from "next";
export default { experimental: { ppr: true } } satisfies NextConfig;

// app/page.tsx
import { Suspense } from "react";
import { StaticShell } from "./components/static-shell";
import { DynamicFeed } from "./components/dynamic-feed";

export default function Page() {
  return (
    <StaticShell>       {/* pre-rendered at build time */}
      <Suspense fallback={<Skeleton />}>
        <DynamicFeed />   {/* streamed dynamically per request */}
      </Suspense>
    </StaticShell>
  );
}
```

**How PPR works:**
1. Static shell is served from CDN (instant, ~0ms TTFB)
2. Dynamic holes streamed via HTTP streaming (React Suspense boundaries)
3. Result: Static page performance + dynamic data freshness

### async/await in Components (App Router)

```tsx
// Server Component — async by default
async function ProductPage({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params;  // MUST await params in Next.js 15
  const product = await db.products.findUnique({ where: { id } });
  
  if (!product) notFound();
  
  return <ProductDetail product={product} />;
}
```

**Breaking change in Next.js 15:** `params` and `searchParams` are now **Promises**. Must be awaited. Code using `params.id` directly (synchronous) breaks.

### Route Handlers and Server Actions

```ts
// app/api/webhooks/stripe/route.ts
import { headers } from "next/headers";
import Stripe from "stripe";

export async function POST(request: Request) {
  const body = await request.text();
  const signature = (await headers()).get("stripe-signature")!;
  
  const event = stripe.webhooks.constructEvent(body, signature, process.env.STRIPE_WEBHOOK_SECRET!);
  
  switch (event.type) {
    case "checkout.session.completed":
      await handleCheckoutCompleted(event.data.object);
      break;
  }
  
  return Response.json({ received: true });
}
```

**Server Actions** (use server directive):
```ts
// app/actions/users.ts
"use server";
import { auth } from "@/auth";
import { db } from "@/db";
import { revalidatePath } from "next/cache";

export async function updateUser(id: string, data: { name: string }) {
  const session = await auth();
  if (!session?.user) throw new Error("Unauthorized");
  
  await db.users.update({ where: { id }, data });
  revalidatePath("/dashboard");
}
```

### Caching in Next.js 15

Next.js 15 made caching **opt-in** (reversed from 14's aggressive default caching):
- Route handlers: NOT cached by default (fetch is dynamic by default)
- `fetch()`: dynamic by default; use `{ cache: "force-cache" }` to opt into caching
- `unstable_cache`: Cache database queries between requests

```ts
import { unstable_cache } from "next/cache";

const getUser = unstable_cache(
  async (userId: string) => db.users.findUnique({ where: { id: userId } }),
  ["user"],
  { revalidate: 60, tags: ["user"] }  // revalidate every 60s or on tag invalidation
);
```

### CVE-2025-29927: The Middleware Bypass

**Critical security vulnerability patched in Next.js 15.2.3** (March 2025):
- Attackers could bypass Next.js middleware (authentication checks, redirects) by setting `x-middleware-subrequest` header
- All middleware-based auth was bypassable without the patch
- **Ensure you are on Next.js 15.2.3 or later**
- **Never rely solely on middleware for authorization** — always validate auth in Server Actions, Route Handlers, and Server Components independently

---

## Drizzle ORM vs. Prisma 7

### Drizzle ORM — The Expert Recommendation for 2025

Drizzle is a TypeScript ORM that generates zero-overhead SQL. Unlike Prisma, Drizzle doesn't use a query engine or generate a client at build time.

**Key advantages:**
- SQL-first: the query you write is the query that runs (no magic)
- No separate build step (Prisma requires `prisma generate`)
- 2–3x faster cold starts (critical for serverless/edge)
- Native support for complex SQL (CTEs, window functions, lateral joins)
- Works at the edge (Cloudflare Workers, Vercel Edge Functions)

```ts
// schema.ts
import { pgTable, uuid, text, timestamp, index } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: uuid("id").defaultRandom().primaryKey(),
  email: text("email").notNull().unique(),
  name: text("name"),
  createdAt: timestamp("created_at").defaultNow().notNull(),
}, (table) => ({
  emailIdx: index("email_idx").on(table.email),
}));

// query.ts
import { db } from "./db";
import { users } from "./schema";
import { eq, desc } from "drizzle-orm";

const recentUsers = await db
  .select()
  .from(users)
  .where(eq(users.role, "admin"))
  .orderBy(desc(users.createdAt))
  .limit(10);
```

**Drizzle migrations:**
```bash
npx drizzle-kit generate  # generate migration SQL files
npx drizzle-kit migrate   # apply migrations
npx drizzle-kit studio    # visual database browser
```

### Prisma 7 — When It's Still the Right Choice

Prisma 7 (released early 2026) rewrote the query engine in Rust and added native serverless support. It closed most of the performance gap with Drizzle.

**When to choose Prisma 7:**
- Team unfamiliar with SQL who benefits from Prisma's ergonomic API
- Projects with complex relations where Prisma's relation queries are more readable
- Prisma Studio for non-technical stakeholders to view/edit data

**Prisma 7 schema:**
```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}
```

---

## TanStack Query v5: Server State Management

TanStack Query (formerly React Query) is the standard for server-state management — API data, cache invalidation, background refetch, optimistic updates.

### Core Patterns

```tsx
// providers.tsx — wrap app
"use client";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,  // 5 minutes
      retry: 2,
    },
  },
});

export function Providers({ children }: { children: React.ReactNode }) {
  return <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>;
}
```

```tsx
// Fetching with useQuery
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

function UserList() {
  const { data: users, isLoading, error } = useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then(r => r.json()),
  });

  const queryClient = useQueryClient();
  
  const createUser = useMutation({
    mutationFn: (data: { name: string }) =>
      fetch("/api/users", { method: "POST", body: JSON.stringify(data) }).then(r => r.json()),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ["users"] }),
    onMutate: async (newUser) => {
      // Optimistic update
      await queryClient.cancelQueries({ queryKey: ["users"] });
      const previous = queryClient.getQueryData(["users"]);
      queryClient.setQueryData(["users"], (old: User[]) => [...old, { ...newUser, id: "temp" }]);
      return { previous };
    },
    onError: (_, __, context) => {
      queryClient.setQueryData(["users"], context?.previous);
    },
  });
}
```

### TanStack Query + Next.js App Router (Hydration Pattern)

```tsx
// Server Component: prefetch + dehydrate
import { dehydrate, HydrationBoundary, QueryClient } from "@tanstack/react-query";

export default async function UsersPage() {
  const queryClient = new QueryClient();
  await queryClient.prefetchQuery({
    queryKey: ["users"],
    queryFn: () => getUsers(),
  });

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <UserList />  {/* Client component has data immediately */}
    </HydrationBoundary>
  );
}
```

---

## Tailwind CSS v4 + shadcn/ui

### Tailwind v4 Breaking Changes

```css
/* OLD (v3): tailwind.config.js file */
/* NEW (v4): CSS-first configuration */

@import "tailwindcss";

@theme {
  --color-brand: oklch(0.62 0.19 259);
  --font-sans: "Inter Variable", sans-serif;
  --radius-lg: 0.75rem;
}
```

**Key v4 changes:**
- Config in CSS (not JS) — `tailwind.config.js` deprecated
- Vite-native engine (10–100x faster builds)
- OKLCH color space by default
- `@utility` directive for custom utilities
- `@variant` for custom variants (replaces `addVariant` in plugin API)
- `text-shadow-*`, `mask-*`, and 3D transform utilities built-in

### shadcn/ui — Component Primitives

shadcn/ui is not a component library — it's a component registry. You copy components into your project and own them completely.

```bash
npx shadcn@latest init
npx shadcn@latest add button card dialog form table
```

**Integration with React Hook Form + Zod:**
```tsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import { Form, FormField, FormItem, FormLabel, FormControl, FormMessage } from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";

const schema = z.object({
  email: z.string().email("Invalid email"),
  name: z.string().min(2, "Name must be at least 2 characters"),
});

function ProfileForm() {
  const form = useForm<z.infer<typeof schema>>({
    resolver: zodResolver(schema),
  });

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl><Input {...field} /></FormControl>
              <FormMessage />  {/* shows Zod errors automatically */}
            </FormItem>
          )}
        />
        <Button type="submit">Save</Button>
      </form>
    </Form>
  );
}
```

---

## Vercel AI SDK 4.x: Building AI Features

The Vercel AI SDK is the standard for integrating LLMs into Next.js apps.

### Streaming Chat

```tsx
// app/api/chat/route.ts
import { anthropic } from "@ai-sdk/anthropic";
import { streamText } from "ai";

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: anthropic("claude-sonnet-4-6"),
    system: "You are a helpful assistant.",
    messages,
    maxTokens: 1024,
  });

  return result.toDataStreamResponse();
}
```

```tsx
// app/chat/page.tsx
"use client";
import { useChat } from "ai/react";

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat();

  return (
    <div className="flex flex-col h-screen">
      <div className="flex-1 overflow-y-auto p-4 space-y-4">
        {messages.map(m => (
          <div key={m.id} className={m.role === "user" ? "text-right" : "text-left"}>
            {m.content}
          </div>
        ))}
      </div>
      <form onSubmit={handleSubmit} className="p-4 border-t">
        <input value={input} onChange={handleInputChange} placeholder="Type a message..." />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  );
}
```

### Structured Object Generation

```ts
import { generateObject } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { z } from "zod";

const { object } = await generateObject({
  model: anthropic("claude-sonnet-4-6"),
  schema: z.object({
    sentiment: z.enum(["positive", "negative", "neutral"]),
    score: z.number().min(0).max(1),
    summary: z.string(),
    topics: z.array(z.string()),
  }),
  prompt: `Analyze this customer review: "${reviewText}"`,
});

// object is fully typed: { sentiment: "positive", score: 0.87, ... }
```

### Tool Calls (AI SDK)

```ts
import { streamText, tool } from "ai";
import { z } from "zod";

const result = streamText({
  model: anthropic("claude-opus-4-8"),
  tools: {
    searchProducts: tool({
      description: "Search the product catalog",
      parameters: z.object({
        query: z.string(),
        category: z.enum(["electronics", "clothing", "food"]).optional(),
      }),
      execute: async ({ query, category }) => {
        return await db.products.search(query, category);
      },
    }),
  },
  messages,
  maxSteps: 5,  // allow up to 5 tool-call rounds
});
```

---

## Authentication: Auth.js v5 (NextAuth)

```ts
// auth.ts
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";
import { DrizzleAdapter } from "@auth/drizzle-adapter";
import { db } from "./db";

export const { auth, handlers, signIn, signOut } = NextAuth({
  adapter: DrizzleAdapter(db),
  providers: [
    GitHub,
    // Resend email magic links
    Resend({ from: "auth@yourdomain.com" }),
  ],
  callbacks: {
    session({ session, user }) {
      session.user.id = user.id;
      return session;
    },
  },
});
```

**Route protection:**
```ts
// middleware.ts — protects routes
export { auth as middleware } from "@/auth";

export const config = {
  matcher: ["/dashboard/:path*", "/api/protected/:path*"],
};
```

**IMPORTANT: CVE-2025-29927 reminder** — middleware alone is insufficient for auth. Always validate the session in each Server Action and Route Handler independently:
```ts
// In every protected server action:
const session = await auth();
if (!session?.user?.id) throw new Error("Unauthorized");
```

---

## Security: Authentication Best Practices

### RFC 9700 — PKCE Mandatory for OAuth 2.1

As of 2026, OAuth 2.1 (RFC 9700) mandates **PKCE (Proof Key for Code Exchange)** for ALL OAuth flows, including confidential clients (server-side web apps). The implicit grant flow is prohibited.

```
Authorization Code Flow with PKCE:
1. Generate code_verifier (random 43-128 char string)
2. code_challenge = BASE64URL(SHA256(code_verifier))
3. Send code_challenge with authorization request
4. Receive authorization code
5. Exchange code + code_verifier for token
6. Server verifies: SHA256(code_verifier) == code_challenge
```

Auth.js v5 and Clerk implement PKCE automatically. If rolling your own OAuth, PKCE is non-negotiable.

### Content Security Policy (CSP)

```tsx
// next.config.ts — CSP header
const cspHeader = `
  default-src 'self';
  script-src 'self' 'unsafe-eval' 'unsafe-inline';
  style-src 'self' 'unsafe-inline';
  img-src 'self' blob: data: https:;
  font-src 'self';
  object-src 'none';
  base-uri 'self';
  form-action 'self';
  frame-ancestors 'none';
  upgrade-insecure-requests;
`;

const nextConfig = {
  async headers() {
    return [{
      source: "/(.*)",
      headers: [{ key: "Content-Security-Policy", value: cspHeader.replace(/\n/g, "") }],
    }];
  },
};
```

---

## Testing Strategy

### Vitest + Testing Library

```ts
// users.test.ts
import { describe, it, expect, vi } from "vitest";
import { render, screen, userEvent } from "@testing-library/react";
import { UserForm } from "./user-form";

describe("UserForm", () => {
  it("shows error when name is empty", async () => {
    render(<UserForm onSubmit={vi.fn()} />);
    
    await userEvent.click(screen.getByRole("button", { name: /submit/i }));
    
    expect(screen.getByText("Name is required")).toBeInTheDocument();
  });
});
```

### Playwright E2E Testing

```ts
// e2e/dashboard.spec.ts
import { test, expect } from "@playwright/test";

test("authenticated user can view dashboard", async ({ page }) => {
  await page.goto("/sign-in");
  await page.fill("[name=email]", "test@example.com");
  await page.fill("[name=password]", "password");
  await page.click("[type=submit]");
  
  await expect(page).toHaveURL("/dashboard");
  await expect(page.getByText("Welcome back")).toBeVisible();
});
```

---

## Performance Patterns

### Core Web Vitals Targets

| Metric | Good | Needs Improvement | Poor |
|---|---|---|---|
| LCP (Largest Contentful Paint) | < 2.5s | 2.5–4.0s | > 4.0s |
| INP (Interaction to Next Paint) | < 200ms | 200–500ms | > 500ms |
| CLS (Cumulative Layout Shift) | < 0.1 | 0.1–0.25 | > 0.25 |

**LCP optimization checklist:**
- Preload the LCP image: `<link rel="preload" as="image" href="/hero.webp">`
- Use `priority` on Next.js `<Image>` component for above-the-fold images
- Use Vercel Edge Network / CDN for static assets

**INP optimization:**
- Move heavy computations to web workers or server actions
- Use `useTransition` to mark non-urgent updates
- Avoid synchronous operations > 50ms in event handlers

### Bundle Analysis

```bash
ANALYZE=true next build
```

Identify large dependencies. Common culprits: `moment.js` (replace with `date-fns`), `lodash` (use individual imports), `@heroicons/react` (import individual icons).

---

## Biome: ESLint + Prettier Replacement

Biome is a Rust-based toolchain that replaces ESLint + Prettier in a single tool, 25–50x faster.

```json
// biome.json
{
  "$schema": "https://biomejs.dev/schemas/1.9.0/schema.json",
  "organizeImports": { "enabled": true },
  "linter": {
    "enabled": true,
    "rules": { "recommended": true }
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2
  }
}
```

```bash
npx @biomejs/biome check --write .  # lint + format + fix
```

**When to still use ESLint:** Teams with custom rules, eslint-plugin-react-hooks (Biome is catching up), or specific plugin ecosystem requirements. For greenfield projects in 2026: Biome.
