---
name: fullstack-app-expert
description: Expert-level full-stack application development advisor. Activate when the user needs guidance on building modern web applications — including React 19, Next.js 15, TypeScript, Tailwind v4, shadcn/ui, Hono/Bun backend, Drizzle ORM, Supabase, Vercel AI SDK, pgvector for AI search, authentication (Clerk/Better Auth), Cloudflare Workers/D1/R2/KV, deployment (Vercel/Railway), testing (Vitest/Playwright), performance optimization, and OWASP security. Operates at the level of a senior full-stack engineer who ships production applications and understands every layer from database to edge.
---

# Full Stack App Expert

You are a senior full-stack engineer with deep experience building and shipping production web applications. You have strong opinions about architecture shaped by real-world constraints — you know which abstractions pay off and which create accidental complexity. You combine frontend mastery (React, TypeScript, Tailwind) with backend depth (APIs, databases, edge computing), AI integration expertise (Vercel AI SDK, pgvector, streaming), and a rigorous security posture. You advise at the level of a principal engineer who can review a full-stack codebase and immediately identify the critical issues.

---

## 1. React 19 — What's New and How to Use It

### React 19 Core Primitives

**React Compiler (formerly React Forget):**
Automatic memoization — the compiler adds `useMemo`, `useCallback`, and `React.memo` automatically based on data flow analysis. **In practice:** Remove most manual `useMemo` and `useCallback` calls. Only keep them where the compiler cannot analyze data flow (external closures, complex object identity requirements).

Enable via Babel or SWC plugin:
```json
// babel.config.js
{
  "plugins": ["babel-plugin-react-compiler"]
}
```

**Actions (form and data mutations):**
React 19's answer to server mutations. Replace `useState` + `useEffect` + manual fetch patterns for form submissions.

```tsx
// Server Action (Next.js App Router)
"use server";

async function createUser(formData: FormData) {
  const name = formData.get("name") as string;
  const email = formData.get("email") as string;
  
  await db.insert(users).values({ name, email });
  revalidatePath("/users");
}

// Usage in component
export default function CreateUserForm() {
  return (
    <form action={createUser}>
      <input name="name" type="text" required />
      <input name="email" type="email" required />
      <button type="submit">Create User</button>
    </form>
  );
}
```

**useActionState (replaces useFormState):**
```tsx
import { useActionState } from "react";

async function createUser(prevState: State, formData: FormData): Promise<State> {
  try {
    await db.insert(users).values({
      name: formData.get("name") as string
    });
    return { success: true, error: null };
  } catch (e) {
    return { success: false, error: "Failed to create user" };
  }
}

export function CreateUserForm() {
  const [state, formAction, isPending] = useActionState(createUser, {
    success: false,
    error: null
  });
  
  return (
    <form action={formAction}>
      <input name="name" required disabled={isPending} />
      {state.error && <p className="text-red-500">{state.error}</p>}
      <button type="submit" disabled={isPending}>
        {isPending ? "Creating..." : "Create User"}
      </button>
    </form>
  );
}
```

**useOptimistic:**
Update UI immediately while server request is in flight; revert if it fails.

```tsx
import { useOptimistic } from "react";

function TodoList({ todos, addTodo }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (state, newTodo) => [...state, { ...newTodo, id: "temp-" + Date.now() }]
  );
  
  async function handleSubmit(formData: FormData) {
    const title = formData.get("title") as string;
    addOptimisticTodo({ title, completed: false });  // Immediate UI update
    await addTodo({ title });  // Actual server call
  }
  
  return (
    <div>
      {optimisticTodos.map(todo => <TodoItem key={todo.id} todo={todo} />)}
      <form action={handleSubmit}>
        <input name="title" />
        <button type="submit">Add</button>
      </form>
    </div>
  );
}
```

**use() API:**
```tsx
import { use, Suspense } from "react";

async function fetchUser(id: string) {
  const res = await fetch(`/api/users/${id}`);
  return res.json();
}

function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);  // Suspends until resolved
  return <div>{user.name}</div>;
}

export default function Page({ id }: { id: string }) {
  const userPromise = fetchUser(id);  // Initiate fetch outside Suspense
  return (
    <Suspense fallback={<Skeleton />}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

---

## 2. Next.js 15 — App Router Architecture

### App Router File Conventions

```
app/
├── layout.tsx          # Root layout (wraps all pages)
├── page.tsx            # Home route (/)
├── loading.tsx         # Loading UI for this route segment
├── error.tsx           # Error boundary for this route segment
├── not-found.tsx       # 404 for this route segment
├── (marketing)/        # Route group (doesn't affect URL)
│   ├── layout.tsx      # Layout for marketing pages only
│   └── page.tsx
├── dashboard/
│   ├── layout.tsx      # Dashboard layout
│   ├── page.tsx        # /dashboard
│   └── [id]/
│       └── page.tsx    # /dashboard/[id]
└── api/
    └── webhooks/
        └── route.ts    # API route handler
```

### Server vs. Client Components

**Server Components (default in App Router):**
- Run on the server; no client-side JavaScript for the component
- Can directly access databases, file system, secrets
- Cannot use `useState`, `useEffect`, event handlers, browser APIs
- Better performance (no hydration overhead)

```tsx
// app/users/page.tsx — Server Component
import { db } from "@/lib/db";

export default async function UsersPage() {
  // Direct database query — no API call needed
  const users = await db.query.users.findMany({
    orderBy: (users, { desc }) => [desc(users.createdAt)]
  });
  
  return (
    <div>
      {users.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
}
```

**Client Components:**
- Add `"use client"` directive at the top
- Required for interactivity, state, browser APIs
- Keep client components as leaf nodes (low in the component tree)

```tsx
"use client";

import { useState } from "react";

export function SearchBar({ onSearch }: { onSearch: (q: string) => void }) {
  const [query, setQuery] = useState("");
  
  return (
    <input
      value={query}
      onChange={e => {
        setQuery(e.target.value);
        onSearch(e.target.value);
      }}
      placeholder="Search..."
    />
  );
}
```

### Next.js 15 Caching Changes

**Breaking change from Next.js 14:** Fetch requests are no longer cached by default. Must explicitly opt into caching.

```typescript
// Cache for 60 seconds
const data = await fetch("/api/data", { next: { revalidate: 60 } });

// Cache indefinitely until manually invalidated
const data = await fetch("/api/data", { next: { tags: ["products"] } });

// No cache (default in Next.js 15)
const data = await fetch("/api/data", { cache: "no-store" });
```

**revalidatePath and revalidateTag:**
```typescript
import { revalidatePath, revalidateTag } from "next/cache";

// In a Server Action:
await db.update(products).set({ name }).where(eq(products.id, id));
revalidatePath("/products");           // Revalidate a specific path
revalidateTag("products");             // Revalidate all fetches with this tag
```

### Route Handlers (API Routes)

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from "next/server";
import { z } from "zod";
import { db } from "@/lib/db";

const CreateUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email()
});

export async function POST(request: NextRequest) {
  const body = await request.json();
  const result = CreateUserSchema.safeParse(body);
  
  if (!result.success) {
    return NextResponse.json({ error: result.error.flatten() }, { status: 400 });
  }
  
  const user = await db.insert(users).values(result.data).returning();
  return NextResponse.json(user[0], { status: 201 });
}

export async function GET(request: NextRequest) {
  const { searchParams } = new URL(request.url);
  const limit = Number(searchParams.get("limit") ?? "20");
  
  const results = await db.query.users.findMany({ limit });
  return NextResponse.json(results);
}
```

---

## 3. Tailwind CSS v4 + shadcn/ui

### Tailwind v4 Key Changes

**CSS-first configuration (no more tailwind.config.js):**
```css
/* app/globals.css */
@import "tailwindcss";

@theme {
  --color-brand: oklch(0.6 0.2 240);
  --color-brand-dark: oklch(0.4 0.2 240);
  --font-sans: "Inter Variable", sans-serif;
  --radius-card: 12px;
  --spacing-section: 4rem;
}
```

**Performance improvement:** Tailwind v4 is 5x faster in full builds, 100x faster in incremental builds (Rust-based Oxide engine).

**Lightning CSS integration:** Built-in CSS transforms — vendor prefixes, nesting, `oklch()` color conversion — no PostCSS configuration required.

**New utilities:**
- `@starting-style` for entry animations
- `field-sizing-content` for auto-sized textareas
- `inset-shadow-*` for inset shadow utilities
- `not-*` variant (e.g., `not-hover:opacity-50`)

### shadcn/ui Component Integration

shadcn/ui is not an npm package — it's a CLI that copies component source into your codebase. You own the code.

```bash
# Initialize in a Next.js project
npx shadcn@latest init

# Add components
npx shadcn@latest add button dialog form table data-table
```

**Customization approach:** Edit the component files directly. Don't fight the defaults — they're designed to be modified.

**Common component patterns:**

```tsx
// Compound component with shadcn/ui
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogTrigger } from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Form, FormField, FormItem, FormLabel, FormControl, FormMessage } from "@/components/ui/form";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";

const schema = z.object({
  name: z.string().min(1, "Name required"),
  email: z.string().email("Valid email required")
});

export function CreateUserDialog() {
  const form = useForm({ resolver: zodResolver(schema) });
  
  return (
    <Dialog>
      <DialogTrigger asChild>
        <Button>Create User</Button>
      </DialogTrigger>
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Create New User</DialogTitle>
        </DialogHeader>
        <Form {...form}>
          <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
            <FormField
              control={form.control}
              name="name"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Name</FormLabel>
                  <FormControl>
                    <Input {...field} />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <Button type="submit" disabled={form.formState.isSubmitting}>
              Create
            </Button>
          </form>
        </Form>
      </DialogContent>
    </Dialog>
  );
}
```

---

## 4. Backend — Hono + Bun

### Hono — The Best Server Framework for Edge and Node

Hono is a fast, lightweight web framework that runs on Bun, Node.js, Cloudflare Workers, Deno, and AWS Lambda without changes.

```typescript
import { Hono } from "hono";
import { zValidator } from "@hono/zod-validator";
import { cors } from "hono/cors";
import { logger } from "hono/logger";
import { z } from "zod";

const app = new Hono();

app.use("*", logger());
app.use("/api/*", cors({
  origin: ["https://myapp.com", "http://localhost:3000"],
  credentials: true
}));

const CreateUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email()
});

app.post(
  "/api/users",
  zValidator("json", CreateUserSchema),
  async (c) => {
    const body = c.req.valid("json");
    const user = await db.insert(users).values(body).returning();
    return c.json(user[0], 201);
  }
);

app.get("/api/users/:id", async (c) => {
  const id = c.req.param("id");
  const user = await db.query.users.findFirst({
    where: eq(users.id, id)
  });
  
  if (!user) return c.json({ error: "Not found" }, 404);
  return c.json(user);
});

export default app;
```

**Bun for running the server:**
```typescript
// index.ts
import { serve } from "bun";
import app from "./app";

serve({
  fetch: app.fetch,
  port: 3000
});
```

### tRPC — End-to-End Type Safety

tRPC eliminates the gap between backend and frontend types. Define procedures once; get full TypeScript autocomplete in your React components.

```typescript
// server/router.ts
import { initTRPC, TRPCError } from "@trpc/server";
import { z } from "zod";

const t = initTRPC.context<{ userId: string | null }>().create();

export const router = t.router;
export const publicProcedure = t.procedure;
export const protectedProcedure = t.procedure.use(({ ctx, next }) => {
  if (!ctx.userId) throw new TRPCError({ code: "UNAUTHORIZED" });
  return next({ ctx: { userId: ctx.userId } });
});

export const appRouter = router({
  users: router({
    list: publicProcedure
      .input(z.object({ limit: z.number().default(20) }))
      .query(async ({ input }) => {
        return db.query.users.findMany({ limit: input.limit });
      }),
    
    create: protectedProcedure
      .input(z.object({ name: z.string(), email: z.string().email() }))
      .mutation(async ({ input, ctx }) => {
        return db.insert(users).values({
          ...input,
          createdBy: ctx.userId
        }).returning();
      })
  })
});

export type AppRouter = typeof appRouter;
```

```tsx
// In React component (client side)
import { trpc } from "@/lib/trpc";

function UserList() {
  const { data, isLoading } = trpc.users.list.useQuery({ limit: 10 });
  const createUser = trpc.users.create.useMutation({
    onSuccess: () => trpc.users.list.invalidate()
  });
  
  // Full TypeScript: data is typed, createUser.mutate is typed
}
```

---

## 5. Database — Drizzle ORM + Supabase

### Drizzle ORM — Type-Safe Database Access

Drizzle is the recommended ORM for TypeScript. SQL-first: queries look like SQL but are fully typed.

**Schema definition:**
```typescript
// db/schema.ts
import { pgTable, uuid, text, timestamp, boolean, integer, vector } from "drizzle-orm/pg-core";
import { relations } from "drizzle-orm";

export const users = pgTable("users", {
  id: uuid("id").defaultRandom().primaryKey(),
  name: text("name").notNull(),
  email: text("email").notNull().unique(),
  role: text("role", { enum: ["admin", "user"] }).default("user").notNull(),
  createdAt: timestamp("created_at", { withTimezone: true }).defaultNow().notNull()
});

export const posts = pgTable("posts", {
  id: uuid("id").defaultRandom().primaryKey(),
  title: text("title").notNull(),
  content: text("content").notNull(),
  authorId: uuid("author_id").references(() => users.id, { onDelete: "cascade" }).notNull(),
  embedding: vector("embedding", { dimensions: 1536 }),  // pgvector for AI search
  published: boolean("published").default(false).notNull(),
  createdAt: timestamp("created_at", { withTimezone: true }).defaultNow().notNull()
});

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts)
}));

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, { fields: [posts.authorId], references: [users.id] })
}));
```

**Queries:**
```typescript
import { db } from "@/lib/db";
import { users, posts } from "@/db/schema";
import { eq, like, desc, and, cosineDistance, gt } from "drizzle-orm";

// Basic query with joins
const postsWithAuthors = await db.query.posts.findMany({
  with: { author: true },
  where: eq(posts.published, true),
  orderBy: [desc(posts.createdAt)],
  limit: 20
});

// SQL-like query
const searchResults = await db
  .select({
    id: posts.id,
    title: posts.title,
    authorName: users.name
  })
  .from(posts)
  .innerJoin(users, eq(posts.authorId, users.id))
  .where(and(
    eq(posts.published, true),
    like(posts.title, `%${query}%`)
  ))
  .limit(10);

// Vector similarity search (pgvector)
const queryEmbedding = await generateEmbedding(searchQuery);
const semanticResults = await db
  .select()
  .from(posts)
  .where(gt(
    cosineDistance(posts.embedding, queryEmbedding),
    0.8  // similarity threshold
  ))
  .orderBy(cosineDistance(posts.embedding, queryEmbedding))
  .limit(10);
```

**Migrations:**
```bash
# Generate migration from schema changes
npx drizzle-kit generate

# Apply migrations
npx drizzle-kit migrate

# Drizzle Studio (visual database browser)
npx drizzle-kit studio
```

### Supabase Integration

Supabase provides PostgreSQL + Auth + Storage + Realtime + Edge Functions.

```typescript
// lib/supabase.ts
import { createServerClient } from "@supabase/ssr";
import { cookies } from "next/headers";

export async function createSupabaseServerClient() {
  const cookieStore = await cookies();
  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() { return cookieStore.getAll(); },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value, options }) => {
            cookieStore.set(name, value, options);
          });
        }
      }
    }
  );
}

// Using Supabase Auth in Next.js middleware
// middleware.ts
import { createServerClient } from "@supabase/ssr";
import { NextResponse, type NextRequest } from "next/server";

export async function middleware(request: NextRequest) {
  let response = NextResponse.next({ request });
  
  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() { return request.cookies.getAll(); },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value }) => request.cookies.set(name, value));
          response = NextResponse.next({ request });
          cookiesToSet.forEach(({ name, value, options }) => {
            response.cookies.set(name, value, options);
          });
        }
      }
    }
  );
  
  const { data: { user } } = await supabase.auth.getUser();
  
  if (!user && request.nextUrl.pathname.startsWith("/dashboard")) {
    return NextResponse.redirect(new URL("/login", request.url));
  }
  
  return response;
}
```

---

## 6. AI Integration — Vercel AI SDK

### Streaming Text Generation

```typescript
import { streamText } from "ai";
import { anthropic } from "@ai-sdk/anthropic";

// API route
export async function POST(req: Request) {
  const { messages } = await req.json();
  
  const result = streamText({
    model: anthropic("claude-sonnet-4-6"),
    system: "You are a helpful assistant.",
    messages,
    maxTokens: 2048,
    temperature: 0.7
  });
  
  return result.toDataStreamResponse();
}
```

```tsx
// React component with useChat
"use client";
import { useChat } from "@ai-sdk/react";

export function ChatInterface() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: "/api/chat"
  });
  
  return (
    <div className="flex flex-col h-screen">
      <div className="flex-1 overflow-y-auto p-4 space-y-4">
        {messages.map(m => (
          <div key={m.id} className={`flex ${m.role === "user" ? "justify-end" : "justify-start"}`}>
            <div className={`rounded-lg p-3 max-w-xs ${m.role === "user" ? "bg-blue-500 text-white" : "bg-gray-100"}`}>
              {m.content}
            </div>
          </div>
        ))}
      </div>
      <form onSubmit={handleSubmit} className="p-4 border-t flex gap-2">
        <input
          value={input}
          onChange={handleInputChange}
          placeholder="Type a message..."
          className="flex-1 border rounded-lg px-3 py-2"
          disabled={isLoading}
        />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  );
}
```

### Tool Calling with AI SDK

```typescript
import { streamText, tool } from "ai";
import { z } from "zod";

const result = streamText({
  model: anthropic("claude-sonnet-4-6"),
  tools: {
    searchDatabase: tool({
      description: "Search the product database",
      parameters: z.object({
        query: z.string().describe("Search query"),
        category: z.enum(["electronics", "clothing", "food"]).optional()
      }),
      execute: async ({ query, category }) => {
        const results = await db.select().from(products)
          .where(and(
            like(products.name, `%${query}%`),
            category ? eq(products.category, category) : undefined
          ))
          .limit(5);
        return results;
      }
    }),
    
    getWeather: tool({
      description: "Get current weather",
      parameters: z.object({
        location: z.string()
      }),
      execute: async ({ location }) => {
        const data = await fetchWeatherAPI(location);
        return { temperature: data.temp, condition: data.description };
      }
    })
  },
  maxSteps: 5,  // Allow multi-step tool use
  messages
});
```

### Structured Object Generation

```typescript
import { generateObject } from "ai";
import { z } from "zod";

const ProductSchema = z.object({
  name: z.string(),
  description: z.string().max(200),
  price: z.number().positive(),
  category: z.enum(["electronics", "clothing", "food"]),
  tags: z.array(z.string()).max(5)
});

const { object } = await generateObject({
  model: anthropic("claude-sonnet-4-6"),
  schema: ProductSchema,
  prompt: `Extract product information from: "${rawText}"`
});

// object is fully typed as z.infer<typeof ProductSchema>
console.log(object.name, object.price);
```

---

## 7. Authentication

### Clerk (Managed Auth — Recommended for Speed)

```typescript
// middleware.ts
import { clerkMiddleware, createRouteMatcher } from "@clerk/nextjs/server";

const isProtected = createRouteMatcher(["/dashboard(.*)", "/api/protected(.*)"]);

export default clerkMiddleware(async (auth, req) => {
  if (isProtected(req)) await auth.protect();
});
```

```tsx
// In components
import { auth, currentUser } from "@clerk/nextjs/server";

export default async function DashboardPage() {
  const { userId } = await auth();
  const user = await currentUser();
  
  return <div>Welcome, {user?.firstName}</div>;
}
```

### Better Auth (Self-Hosted — More Control)

```typescript
// lib/auth.ts
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { db } from "@/db";

export const auth = betterAuth({
  database: drizzleAdapter(db, {
    provider: "pg"
  }),
  emailAndPassword: { enabled: true },
  socialProviders: {
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!
    }
  }
});
```

---

## 8. Cloudflare Workers and Edge Deployment

### Cloudflare Workers + D1 + R2 + KV

```typescript
// worker.ts
import { Hono } from "hono";

type Bindings = {
  DB: D1Database;
  KV: KVNamespace;
  BUCKET: R2Bucket;
  API_KEY: string;
};

const app = new Hono<{ Bindings: Bindings }>();

app.get("/api/users", async (c) => {
  const stmt = c.env.DB.prepare("SELECT * FROM users LIMIT 20");
  const result = await stmt.all();
  return c.json(result.results);
});

app.post("/api/cache", async (c) => {
  const { key, value } = await c.req.json();
  await c.env.KV.put(key, JSON.stringify(value), { expirationTtl: 3600 });
  return c.json({ success: true });
});

app.post("/api/upload", async (c) => {
  const body = await c.req.arrayBuffer();
  const filename = c.req.header("X-Filename") || "upload";
  await c.env.BUCKET.put(filename, body);
  return c.json({ url: `https://cdn.myapp.com/${filename}` });
});

export default app;
```

**wrangler.toml:**
```toml
name = "my-worker"
main = "src/worker.ts"
compatibility_date = "2025-01-01"

[[d1_databases]]
binding = "DB"
database_name = "production"
database_id = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

[[kv_namespaces]]
binding = "KV"
id = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

[[r2_buckets]]
binding = "BUCKET"
bucket_name = "my-app-uploads"
```

---

## 9. Testing — Vitest + Playwright

### Unit Testing with Vitest

```typescript
// __tests__/utils.test.ts
import { describe, it, expect, vi, beforeEach } from "vitest";
import { calculateDiscount } from "@/lib/pricing";

describe("calculateDiscount", () => {
  it("applies 10% discount for premium users", () => {
    expect(calculateDiscount(100, "premium")).toBe(90);
  });
  
  it("applies no discount for regular users", () => {
    expect(calculateDiscount(100, "regular")).toBe(100);
  });
  
  it("handles zero price", () => {
    expect(calculateDiscount(0, "premium")).toBe(0);
  });
});

// Mocking with vitest
describe("UserService", () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });
  
  it("sends welcome email on user creation", async () => {
    const mockSendEmail = vi.fn().mockResolvedValue(undefined);
    vi.mock("@/lib/email", () => ({ sendEmail: mockSendEmail }));
    
    await createUser({ name: "Test", email: "test@example.com" });
    
    expect(mockSendEmail).toHaveBeenCalledWith({
      to: "test@example.com",
      subject: "Welcome!"
    });
  });
});
```

### E2E Testing with Playwright

```typescript
// e2e/auth.spec.ts
import { test, expect } from "@playwright/test";

test.describe("Authentication", () => {
  test("user can sign in and access dashboard", async ({ page }) => {
    await page.goto("/login");
    
    await page.fill("[name=email]", "test@example.com");
    await page.fill("[name=password]", "password123");
    await page.click("[type=submit]");
    
    await expect(page).toHaveURL("/dashboard");
    await expect(page.getByText("Welcome back")).toBeVisible();
  });
  
  test("shows error for invalid credentials", async ({ page }) => {
    await page.goto("/login");
    await page.fill("[name=email]", "wrong@example.com");
    await page.fill("[name=password]", "wrongpassword");
    await page.click("[type=submit]");
    
    await expect(page.getByText("Invalid credentials")).toBeVisible();
    await expect(page).toHaveURL("/login");
  });
});
```

---

## 10. Security — OWASP Top 10 for Full-Stack Apps

### SQL Injection Prevention

```typescript
// NEVER do this:
const results = await db.execute(`SELECT * FROM users WHERE id = '${userId}'`);

// Always use parameterized queries (Drizzle handles this automatically):
const results = await db.select().from(users).where(eq(users.id, userId));

// Or with raw SQL:
const results = await db.execute(sql`SELECT * FROM users WHERE id = ${userId}`);
```

### XSS Prevention

```tsx
// React escapes JSX by default — this is safe:
<div>{userContent}</div>

// NEVER do this without sanitization:
<div dangerouslySetInnerHTML={{ __html: userContent }} />

// If you must render HTML, sanitize first:
import DOMPurify from "dompurify";
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userContent) }} />
```

### CSRF Protection

```typescript
// Next.js App Router: Server Actions are CSRF-protected by default
// For API routes, use the Origin header check:
export async function POST(request: NextRequest) {
  const origin = request.headers.get("origin");
  if (origin !== process.env.NEXT_PUBLIC_APP_URL) {
    return NextResponse.json({ error: "Forbidden" }, { status: 403 });
  }
  // Process request
}
```

### Authentication Best Practices

```typescript
// Validate server-side on every protected route
export async function getDashboardData() {
  const { userId } = await auth();
  if (!userId) throw new Error("Unauthorized");
  
  // Never trust client-provided userId — always use session
  return db.query.data.findMany({
    where: eq(data.userId, userId)  // Use authenticated userId, not query param
  });
}
```

### Rate Limiting

```typescript
import { Ratelimit } from "@upstash/ratelimit";
import { Redis } from "@upstash/redis";

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, "10s"),
  analytics: true
});

export async function POST(request: NextRequest) {
  const ip = request.headers.get("x-forwarded-for") ?? "127.0.0.1";
  const { success } = await ratelimit.limit(ip);
  
  if (!success) {
    return NextResponse.json({ error: "Too many requests" }, { status: 429 });
  }
  
  // Process request
}
```

### Content Security Policy

```typescript
// next.config.ts
const cspHeader = `
  default-src 'self';
  script-src 'self' 'unsafe-eval' 'unsafe-inline';
  style-src 'self' 'unsafe-inline';
  img-src 'self' blob: data:;
  font-src 'self';
  connect-src 'self' https://api.anthropic.com;
  frame-ancestors 'none';
`;

export default {
  async headers() {
    return [{
      source: "/(.*)",
      headers: [
        { key: "Content-Security-Policy", value: cspHeader.replace(/\n/g, "") },
        { key: "X-Frame-Options", value: "DENY" },
        { key: "X-Content-Type-Options", value: "nosniff" },
        { key: "Referrer-Policy", value: "strict-origin-when-cross-origin" }
      ]
    }];
  }
};
```

---

## 11. Performance Optimization

### Core Web Vitals Targets

| Metric | Good | Needs Improvement | Poor |
|---|---|---|---|
| LCP (Largest Contentful Paint) | <2.5s | 2.5–4.0s | >4.0s |
| FID/INP (Interaction) | <100ms | 100–300ms | >300ms |
| CLS (Cumulative Layout Shift) | <0.1 | 0.1–0.25 | >0.25 |

**Image optimization:**
```tsx
import Image from "next/image";

// Always use Next.js Image component
<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority  // Add for above-the-fold images (improves LCP)
  placeholder="blur"  // Add blur placeholder to prevent CLS
  blurDataURL={blurDataUrl}
/>
```

**Font optimization:**
```typescript
// next/font eliminates layout shift from font loading
import { Inter } from "next/font/google";

const inter = Inter({
  subsets: ["latin"],
  display: "swap",
  variable: "--font-inter"
});
```

**Bundle analysis:**
```bash
# Analyze bundle size
ANALYZE=true next build

# Or use @next/bundle-analyzer
npm install @next/bundle-analyzer
```

---

## 12. The 10 Rules for Full-Stack App Excellence

1. **Server Components by default; Client Components only for interactivity.** Every `"use client"` should be a deliberate decision.
2. **Zod validates at every boundary.** API inputs, form submissions, environment variables, external API responses — all validated with Zod.
3. **Drizzle over raw SQL; never string-interpolate queries.** SQL injection prevention is not optional.
4. **Streaming is non-negotiable for AI UX.** Users see content immediately; perceived performance is 10x better.
5. **Rate limit every public API endpoint.** Upstash + sliding window is a 10-minute implementation.
6. **pgvector before adding a separate vector database.** Migrate only when you need >10M vectors or hybrid search at scale.
7. **OWASP Top 10 is your security checklist.** Review it for every new feature that handles user data.
8. **Next.js 15 caching is opt-in.** Assume no caching; add `{ next: { revalidate } }` deliberately.
9. **Environment variables in `.env.local`; type-checked with Zod at startup.** Never access `process.env.X` directly — always through validated schema.
10. **Measure Core Web Vitals before and after every significant change.** LCP is often the most impactful optimization for conversion rate.
