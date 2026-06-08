# Frontend Frameworks & Rendering Patterns (2025–2026)

## React 19 — What Changed and Why It Matters

React 19 (stable Dec 2024, 19.2 released Oct 2025) is the most significant React release since hooks.

### The React Compiler (Stable Since Late 2025)

The React Compiler analyzes your components at build time and automatically inserts memoization where needed. It has been running in production at Meta (Meta Quest Store) since mid-2025 with 20–30% reduction in render time. Next.js 16 ships it enabled by default; Expo SDK 54 enabled it by default in templates.

**Practical impact:**
- Stop writing `useMemo`/`useCallback` defensively. The compiler handles it.
- Keep `useMemo`/`useCallback` only as escape hatches (e.g., when a memoized value is an effect dependency you want to keep stable across renders).
- Existing code: leave manual memoization in place; remove carefully after testing.

Enable in Next.js 15:
```js
// next.config.js
const nextConfig = {
  experimental: { reactCompiler: true }
}
```

### Server Components (RSC) — The Mental Model

Server Components render exclusively on the server — they can be async, can directly `await` database queries or fetch calls, and ship zero JavaScript to the client.

```tsx
// This component never reaches the browser as JS
// It's HTML by the time the client sees it
export default async function ProductList() {
  const products = await db.query.products.findMany(); // Direct DB access
  return (
    <ul>
      {products.map(p => <li key={p.id}>{p.name}</li>)}
    </ul>
  );
}
```

**RSC rules:**
- Server Components: no hooks, no browser APIs, no event handlers. Can be async.
- Client Components: add `'use client'` directive. Can use hooks and event handlers.
- Composition: Server Components CAN render Client Components. Client Components CANNOT render Server Components directly (pass as props instead).

**Performance impact:** RSC reduces client JS bundle, lowering INP scores. Netflix, Vercel, and Shopify all report measurable LCP and INP improvements after migrating to RSC.

### React 19 Actions

Actions replace the boilerplate of `useState` + `useEffect` + form submission handling.

```tsx
// Server Action (in Next.js) — runs only on server
async function createPost(prevState: any, formData: FormData) {
  'use server';
  const title = formData.get('title') as string;
  await db.insert(posts).values({ title });
  revalidatePath('/posts');
}

// Client Component using the action
'use client';
import { useActionState } from 'react';

function PostForm() {
  const [state, action, isPending] = useActionState(createPost, null);
  return (
    <form action={action}>
      <input name="title" />
      <button disabled={isPending}>
        {isPending ? 'Saving...' : 'Create Post'}
      </button>
      {state?.error && <p>{state.error}</p>}
    </form>
  );
}
```

**Key new hooks:**
- `useActionState(action, initialState)` — tracks action lifecycle (pending/success/error)
- `useFormStatus()` — child component accesses parent form's submission status (no prop drilling)
- `useOptimistic(state, updateFn)` — show immediate UI feedback while server processes

```tsx
// useOptimistic pattern
function LikeButton({ postId, initialLikes }) {
  const [likes, addOptimisticLike] = useOptimistic(
    initialLikes,
    (state) => state + 1
  );
  
  async function handleLike() {
    addOptimisticLike(); // Immediate UI update
    await likePost(postId); // Server call (may take 300ms)
  }
  
  return <button onClick={handleLike}>{likes} likes</button>;
}
```

### The `use()` Hook

Unlike regular hooks, `use()` can be called conditionally:

```tsx
// Unwrap a Promise inside a component
function UserName({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise); // Suspends until resolved
  return <span>{user.name}</span>;
}

// Read Context conditionally
function Component({ showTheme }) {
  if (showTheme) {
    const theme = use(ThemeContext); // Valid — hooks can't do this
    return <div style={{ color: theme.primary }}>...</div>;
  }
  return <div>...</div>;
}
```

---

## Next.js 15 / 16

### Caching Model Overhaul (Breaking Change in 15)

Next.js 15 **reversed the default caching behavior**:
- `fetch()` is now **uncached by default** (was cached in v13/14)
- GET Route Handlers are **uncached by default**
- Client Router Cache is **uncached by default**

To opt into caching:
```tsx
// Opt into caching for a specific fetch
const data = await fetch('/api/data', { cache: 'force-cache' });

// Or use revalidation
const data = await fetch('/api/data', { next: { revalidate: 60 } });

// Route-level cache config
export const dynamic = 'force-static'; // in page.tsx
```

### Partial Prerendering (PPR)

PPR is the most important Next.js architectural primitive since App Router. It lets you mix static and dynamic content in the same response — the static shell is served from CDN edge instantly, dynamic parts stream in.

```tsx
// next.config.js
const nextConfig = {
  experimental: { ppr: 'incremental' }
};

// page.tsx — opt in per-route
export const experimental_ppr = true;

export default function ProductPage() {
  return (
    <div>
      <StaticHeader />       {/* Pre-rendered at build time */}
      <Suspense fallback={<Skeleton />}>
        <DynamicReviews />   {/* Streamed after initial response */}
      </Suspense>
    </div>
  );
}
```

**When to use PPR:** Product pages, blog posts, dashboards — anything with a cacheable shell and dynamic personalized sections.

### `after()` API (Stabilized in Next.js 16)

Run code after the response streams to the client — ideal for analytics, logging, side effects:

```tsx
import { after } from 'next/server';

export async function POST(req: Request) {
  const data = await processRequest(req);
  
  after(async () => {
    // Runs AFTER response is sent — doesn't block TTFB
    await analytics.track('form_submitted', data);
    await updateSearchIndex(data);
  });
  
  return Response.json({ success: true });
}
```

### Turbopack

Next.js 15 ships Turbopack for `next dev` as stable. Key numbers:
- 45.8% faster initial route compilation (vs webpack)
- No disk caching yet (in development as of mid-2026)
- Production builds (`next build`) still use webpack by default; Turbopack production is experimental in Next.js 16

---

## Framework Selection Guide

### Next.js 15+ — Use When:
- Team knows React
- You need the full spectrum: SSG, SSR, ISR, streaming, PPR, API routes, edge functions
- You want the best-in-class Next.js + Vercel DX
- Building a SaaS, e-commerce, or content platform

**Tradeoff:** Largest bundle, most complex mental model (RSC boundaries, caching model)

### SvelteKit 2 — Use When:
- Performance is paramount (50–70% less JS than React equivalents)
- Team is flexible on framework
- Building data-heavy dashboards, real-time apps, or anything where INP matters
- Svelte 5 "Runes" system gives fine-grained reactivity without a virtual DOM

```svelte
<!-- Svelte 5 Runes — reactive without VDOM -->
<script>
  let count = $state(0);  // Reactive primitive
  let doubled = $derived(count * 2);  // Computed
</script>
<button onclick={() => count++}>{count} (doubled: {doubled})</button>
```

### Astro 5 — Use When:
- Mostly-static content: blogs, docs, marketing sites, e-commerce storefronts
- Maximum performance out of the box (zero client JS by default)
- Content Collections for type-safe content management
- Islands architecture: sprinkle interactivity where needed (React, Vue, Svelte components)

```astro
---
// Zero JS to the client unless you use client:load
import HeavyChart from './HeavyChart.tsx';
---
<HeavyChart client:visible />  <!-- Only hydrates when in viewport -->
```

**Note:** Cloudflare acquired Astro in January 2026, signaling deep integration with Cloudflare Workers ecosystem.

### Remix / React Router 7 — Use When:
- Form-heavy, mutation-heavy apps
- Want web standards (FormData, Response, Request) first
- Progressive enhancement matters (works without JS)
- Merged in 2024: Remix became React Router 7

```tsx
// Remix loader + action pattern — web standards
export async function loader({ params }) {
  return json(await getUser(params.id));
}

export async function action({ request }) {
  const formData = await request.formData();
  await updateUser(formData);
  return redirect('/users');
}
```

---

## Rendering Patterns Quick Reference

| Pattern | When to Use | Next.js API |
|---|---|---|
| SSG (Static) | Content that never changes or changes rarely | `export const dynamic = 'force-static'` |
| ISR | Content that changes but can be stale for N seconds | `fetch(url, { next: { revalidate: N } })` |
| SSR | Fully dynamic, user-specific, must be fresh | `export const dynamic = 'force-dynamic'` |
| Streaming | Large pages where some parts are slow | `<Suspense>` wrappers |
| PPR | Pages with static shell + dynamic sections | `experimental_ppr = true` |
| CSR | Highly interactive widgets, after hydration | `'use client'` + TanStack Query |

**The 2026 mental model:** Start with RSC (static/server), wrap dynamic sections in `<Suspense>`, use `'use client'` only for interactivity. PPR is the end state — static shell at the edge, dynamic content streamed.
