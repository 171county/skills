---
name: full-stack-app-expert
description: Expert-level full-stack application development advisor for 2024-2025. Invoke for React 19, Next.js 15/16, TypeScript, Tailwind CSS v4, shadcn/ui, database design (Drizzle vs Prisma, pgvector), authentication (Clerk/Auth.js/Better Auth), API design (tRPC, Hono, REST), deployment (Vercel/Railway/Fly.io), performance optimization, AI integration (Vercel AI SDK), security, and testing. Use whenever a user is building or maintaining a modern web application.
---

# Full Stack App Expert

You are a senior staff engineer who builds production web applications with modern TypeScript stacks. You have deep knowledge of the current ecosystem (React 19, Next.js 15, Bun, Drizzle, shadcn/ui) and practical production experience.

---

## Modern Frontend Stack

### React 19 (Stable: December 5, 2024)
Major new capabilities:

**Actions — Async form/mutation handling**
```typescript
// useActionState replaces useFormState
const [state, dispatch, isPending] = useActionState(
  async (prevState, formData) => {
    const result = await createPost(formData.get("title"));
    return result.error ? { error: result.error } : { success: true };
  },
  { error: null }
);
```

**useOptimistic — Optimistic UI**
```typescript
const [optimisticPosts, addOptimisticPost] = useOptimistic(
  posts,
  (currentPosts, newPost) => [...currentPosts, { ...newPost, pending: true }]
);
```

**`use()` hook — Read promises and context in render**
```typescript
// Suspend on unresolved promise (replaces useEffect + useState pattern)
const user = use(userPromise);  // suspends if pending
const theme = use(ThemeContext); // reads context (replaces useContext)
```

**React Compiler (Experimental)** — automatic memoization; replaces manual `useMemo`/`useCallback` in many cases. Enable per-component or project-wide.

**Native document metadata** — `<title>`, `<meta>`, `<link>` in components (no more react-helmet/next/head).

**No more `forwardRef`** — refs passed directly as props in React 19.

### Next.js 15 (October 2024)
| Feature | Status | Notes |
|---------|--------|-------|
| **Turbopack** (dev) | Stable | 45.8% faster initial compile |
| **Turbopack** (prod) | Stable in 15.x | New default bundler |
| **Partial Prerendering (PPR)** | Experimental | Combine SSG + SSR; Suspense-boundary opt-in |
| **Async Request APIs** | Breaking | `cookies()`, `headers()`, `params` now Promises |
| **React 19 support** | Full | App Router: React 19; Pages: React 18 compat |
| **Default fetch caching** | **Breaking** | Changed to `no-store` (was `force-cache` in Next 14) |
| **`next/form`** | New | Enhanced HTML forms with client-side navigation |

**Next.js 16**: React 19 + TypeScript 5 + Tailwind v4 stack by default.

```typescript
// Next.js 15: async params (breaking change)
export default async function Page({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params;  // must await
  return <div>{id}</div>;
}
```

---

## TypeScript & Type Safety Stack

### tRPC v11 (March 2025)
End-to-end type-safe APIs; 2.5M weekly downloads, 36K GitHub stars.

```typescript
// server/router.ts
import { initTRPC } from "@trpc/server";
import { z } from "zod";

const t = initTRPC.create();
export const router = t.router({
  getUser: t.procedure
    .input(z.object({ id: z.string() }))
    .query(async ({ input }) => {
      return db.users.findById(input.id);
    }),
  createPost: t.procedure
    .input(z.object({ title: z.string(), body: z.string() }))
    .mutation(async ({ input }) => {
      return db.posts.create(input);
    }),
});

// v11: Native TanStack Query integration
// client/trpc.ts
import { createTRPCReact } from "@trpc/react-query";
export const trpc = createTRPCReact<AppRouter>();
```

### Hono.js (Edge-First)
- 23K-27K GitHub stars; ~2M weekly downloads; 13KB bundle
- Runs on Cloudflare Workers, Bun, Deno, Node, Vercel Edge, AWS Lambda
- Only major framework targeting all JS runtimes with Web Standards APIs
```typescript
import { Hono } from "hono";
import { zValidator } from "@hono/zod-validator";
import { z } from "zod";

const app = new Hono();

app.get("/users/:id", zValidator("param", z.object({ id: z.string() })),
  async (c) => {
    const { id } = c.req.valid("param");
    const user = await db.users.findById(id);
    return c.json(user);
  }
);

export default app;
```

---

## Database Layer

### Drizzle ORM vs. Prisma (2025)

| Metric | Drizzle | Prisma (v7+) |
|--------|---------|-------------|
| Bundle size | ~7.4 KB | ~180 KB (Prisma 7 pure TS) |
| Cold start (serverless) | ~45ms | ~320ms |
| Edge runtime | Native ✅ | Added in v7.2 |
| Schema location | TypeScript file | `.prisma` file |
| Migration | drizzle-kit | prisma migrate |
| Query style | SQL-like, explicit | High-level, auto-joins |
| Cloudflare Workers | ✅ | Limited |

**Choose Drizzle**: edge runtimes, Cloudflare Workers, serverless cold starts, SQL-comfortable team.
**Choose Prisma**: maximum abstraction, large team, mature tooling needs (Studio), non-edge Node.js.

```typescript
// Drizzle schema definition
import { pgTable, text, timestamp, uuid } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: uuid("id").primaryKey().defaultRandom(),
  email: text("email").notNull().unique(),
  createdAt: timestamp("created_at").defaultNow(),
});

// Query
const user = await db.select().from(users).where(eq(users.email, email)).get();
```

### pgvector for AI Applications
PostgreSQL extension for vector similarity search; ideal for RAG in existing Postgres stack.

```sql
-- Create table with vector column
CREATE EXTENSION vector;
CREATE TABLE documents (
  id SERIAL PRIMARY KEY,
  content TEXT,
  embedding vector(1536)  -- OpenAI embedding dimensions
);

-- HNSW index (recommended for production)
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64);

-- Cosine similarity search
SELECT id, content, 1 - (embedding <=> $1) AS similarity
FROM documents
ORDER BY embedding <=> $1
LIMIT 5;
```

**Index types**: HNSW (production: best speed-recall; ~1.5ms query); IVFFlat (dev/budget: faster build; ~2.4ms query). HNSW can be built on empty tables; IVFFlat requires data first.

**Hybrid search**: combine pgvector semantic search + PostgreSQL full-text search with Reciprocal Rank Fusion (RRF) for best retrieval quality.

### Serverless Database Options
| Service | Best For | Key Feature |
|---------|----------|-------------|
| **Neon** | Serverless Postgres | Scale-to-zero; branching; autosuspend |
| **Supabase** | Full platform | Auth + DB + realtime + storage |
| **PlanetScale** | MySQL serverless | Branching; no foreign keys required |
| **Turso** | Edge SQLite | Per-database pricing; low latency |

---

## Authentication (2025 State)

### Tool Comparison

| Feature | Clerk | Supabase Auth | Better Auth (New Default) |
|---------|-------|---------------|--------------------------|
| Free tier | 10K MAU | 50K MAU | Unlimited (self-hosted) |
| Paid | $0.02/MAU | $0.00325/MAU | $0 (OSS) |
| Setup time | Minutes | 30-60 min | Hours |
| Pre-built UI | ✅ Clerk components | ❌ | ❌ |
| Next.js App Router | Best-in-class | Good | Good |
| Org/team support | Built-in | Manual | Built-in |
| MFA | Built-in | Built-in | Built-in |
| SSO/SAML | Paid add-on | $599/mo | Via plugin |

**2025 Note**: Auth.js (NextAuth) team joined Better Auth project (late 2025). Auth.js receives security patches only; new projects should use Better Auth.

```typescript
// Better Auth setup (new default OSS choice)
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";

export const auth = betterAuth({
  database: drizzleAdapter(db, { provider: "pg" }),
  emailAndPassword: { enabled: true },
  socialProviders: {
    github: { clientId: process.env.GITHUB_CLIENT_ID!, clientSecret: process.env.GITHUB_CLIENT_SECRET! }
  },
});
```

---

## Styling: Tailwind CSS v4 (Stable: January 22, 2025)

### Key Changes from v3
| Change | Details |
|--------|---------|
| No `tailwind.config.js` | All config in CSS via `@theme {}` directive |
| New engine | Lightning CSS (Rust-based); 3.5× faster full builds; 100× faster unchanged |
| Import syntax | `@import "tailwindcss"` (replaces `@tailwind directives`) |
| Color space | OKLCH (vs. HSL in v3) |
| Container queries | Built-in (was plugin) |
| 3D transforms | Built-in |

```css
/* tailwind.css (v4) */
@import "tailwindcss";

@theme {
  --color-brand: oklch(0.7 0.2 230);
  --font-sans: "Inter", sans-serif;
  --radius-lg: 0.75rem;
}
```

### shadcn/ui (2025)
Latest major updates:
- Full Tailwind v4 support (February 2025)
- `new-york` style is the new default (old `default` style deprecated)
- HSL colors → OKLCH
- `tw-animate-css` replaces `tailwindcss-animate`
- `forwardRef` removed (React 19)
- `data-slot` attribute on all primitives for targeted styling
- `sonner` is the new default toast (replacing old toast)

---

## Deployment (2025 Cost Comparison)

| Feature | Vercel | Railway | Fly.io |
|---------|--------|---------|--------|
| Free tier | Hobby (non-commercial) | $5 trial | Removed 2024 |
| Entry paid | $20/seat/month (Pro) | $5/month + usage | ~$10.70/month |
| Best for | Next.js apps | Any app, variable traffic | Multi-region, cost control |
| Database | Vercel Postgres (pricey) | Managed Postgres | Self-managed |
| Regions | Edge globally | Limited | 35+ regions |
| Complexity | Zero config for Next.js | Low | Higher (more DevOps) |

**Selection guide**:
- Side projects/prototypes: Railway (pay only for active compute)
- Next.js production apps (<$500/mo budget): Vercel (zero config drift)
- Production apps needing cost control + multi-region: Fly.io

### Runtime Selection: Bun vs. Node.js

| Metric | Bun 1.x | Node.js 22+ |
|--------|---------|------------|
| Startup time | 8-15ms | 40-120ms |
| HTTP req/s (synthetic) | 100K+ | 25-30K |
| Real-world DB-heavy | ~22ms median | ~23ms median |
| Production maturity | "Production-ready" (Dec 2025) | Extremely mature |
| Ecosystem | Growing; Node compat | Industry standard |

**Use Bun for**: new projects, serverless functions (cold start savings), Hono-based APIs.
**Use Node.js for**: existing projects, libraries with native bindings, maximum ecosystem compatibility.

---

## AI Integration in Full-Stack Apps

### Vercel AI SDK (Best for Next.js)
Version history: AI SDK 4.0 (late 2024), AI SDK 4.2, AI SDK 6 (2025).

```typescript
// app/api/chat/route.ts (Next.js App Router + AI SDK)
import { streamText, generateObject } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { z } from "zod";

// Streaming text
export async function POST(req: Request) {
  const { messages } = await req.json();
  const result = streamText({
    model: anthropic("claude-sonnet-4-6"),
    messages,
    maxSteps: 5  // Multi-step tool use (v4.0 feature)
  });
  return result.toDataStreamResponse();
}

// Structured object generation
const product = await generateObject({
  model: anthropic("claude-haiku-4-5-20251001"),
  schema: z.object({
    name: z.string(),
    price: z.number(),
    category: z.string()
  }),
  prompt: "Extract product details from: " + description
});
```

```typescript
// React hook (Client component)
"use client";
import { useChat } from "ai/react";

export function ChatUI() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat();
  return (
    <form onSubmit={handleSubmit}>
      {messages.map(m => <div key={m.id}>{m.content}</div>)}
      <input value={input} onChange={handleInputChange} disabled={isLoading} />
      <button type="submit">Send</button>
    </form>
  );
}
```

### AI SDK Supported Providers (16+)
Anthropic, OpenAI, Google Generative AI, Groq, Mistral, Cohere, xAI Grok, Amazon Bedrock, Azure, Together.ai, Fireworks, and more — all through unified API.

### pgvector + RAG in Next.js
```typescript
// Semantic search API endpoint
export async function POST(req: Request) {
  const { query } = await req.json();
  
  // Generate query embedding
  const embedding = await generateEmbedding(query);  // OpenAI or local model
  
  // Vector search with pgvector
  const results = await db.execute(sql`
    SELECT content, 1 - (embedding <=> ${embedding}::vector) AS similarity
    FROM documents
    WHERE 1 - (embedding <=> ${embedding}::vector) > 0.7
    ORDER BY similarity DESC
    LIMIT 5
  `);
  
  // Generate response with context
  const { text } = await generateText({
    model: anthropic("claude-sonnet-4-6"),
    prompt: `Context: ${results.rows.map(r => r.content).join("\n")}\n\nQuestion: ${query}`
  });
  
  return Response.json({ answer: text, sources: results.rows });
}
```

---

## Performance Optimization

### Core Web Vitals Targets
- **LCP** (Largest Contentful Paint): <2.5s (good), <4s (needs improvement)
- **CLS** (Cumulative Layout Shift): <0.1 (good)
- **INP** (Interaction to Next Paint, replaced FID): <200ms (good)

### Key Optimization Techniques
1. **Image optimization**: Next.js `<Image>` component — automatic WebP/AVIF, lazy loading, size hints
2. **Font optimization**: `next/font` — eliminates CLS from font loading; self-hosted
3. **Code splitting**: dynamic imports `const Component = dynamic(() => import('./Heavy'))` 
4. **CDN**: Vercel Edge Network automatically; or Cloudflare CDN
5. **Database**: connection pooling (PgBouncer, Neon serverless pooler); indexed queries; avoid N+1
6. **Caching**: React `cache()`, Next.js `unstable_cache`, Redis for expensive computations

---

## Security (OWASP Top 10 for Modern Web Apps)

### Critical Security Patterns

**SQL Injection Prevention** (use parameterized queries):
```typescript
// ✅ Safe: Drizzle/Prisma always parameterize
const user = await db.select().from(users).where(eq(users.id, userId));
// ❌ Never: string interpolation
db.execute(`SELECT * FROM users WHERE id = ${userId}`);
```

**XSS Prevention**:
- React escapes all JSX by default — safe
- Dangerous: `dangerouslySetInnerHTML={{ __html: userContent }}` — never without sanitization
- Sanitize user-generated HTML: DOMPurify

**CSRF Protection**:
- Next.js 15 Server Actions include CSRF protection by default
- For REST APIs: verify `Origin` header; use SameSite cookies

**Authentication Security**:
- HTTPS only; secure + httpOnly + SameSite cookies
- Rotate session tokens after privilege escalation
- Rate limit auth endpoints (Upstash Redis)

**Content Security Policy**:
```typescript
// next.config.ts
const securityHeaders = [
  { key: "X-DNS-Prefetch-Control", value: "on" },
  { key: "Strict-Transport-Security", value: "max-age=63072000" },
  { key: "X-Frame-Options", value: "SAMEORIGIN" },
  { key: "Content-Security-Policy", value: "default-src 'self'; script-src 'self' 'nonce-{nonce}'" }
];
```

**Dependency auditing**:
```bash
npm audit --audit-level=high
npx snyk test
```

---

## Testing Strategy

| Test Type | Tool | When |
|-----------|------|------|
| Unit | Vitest (Bun-native), Jest | Pure functions, utilities |
| Component | Testing Library + Vitest | React components in isolation |
| Integration | Vitest + real DB | API routes, DB queries |
| E2E | Playwright (recommended), Cypress | Full user flows |
| Visual regression | Chromatic, Percy | UI component changes |
| AI features | DeepEval + Promptfoo | LLM output quality |

```typescript
// Playwright E2E example
import { test, expect } from "@playwright/test";

test("user can create and view post", async ({ page }) => {
  await page.goto("/");
  await page.getByRole("link", { name: "Create Post" }).click();
  await page.getByLabel("Title").fill("My Post");
  await page.getByRole("button", { name: "Submit" }).click();
  await expect(page.getByText("My Post")).toBeVisible();
});
```

---

## Reference Files

- [Next.js App Router Patterns](./references/nextjs-patterns.md)
- [Database Schema Design Guide](./references/database-design.md)
- [Authentication Implementation Guide](./references/auth-guide.md)
- [AI Integration Patterns](./references/ai-integration.md)
