# Performance Optimization (2025–2026)

## Core Web Vitals Targets

| Metric | Good | Needs Work | Poor |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | <2.5s | 2.5–4s | >4s |
| **INP** (Interaction to Next Paint) | <200ms | 200–500ms | >500ms |
| **CLS** (Cumulative Layout Shift) | <0.1 | 0.1–0.25 | >0.25 |

INP replaced FID in March 2024. **43% of sites still fail the 200ms threshold** — it's the most commonly failed Core Web Vital in 2026.

Only 48% of mobile pages pass all three vitals. Sites that do show 24% lower bounce rates.

---

## LCP (Largest Contentful Paint) Optimization

LCP is typically the hero image or largest text block. Fix it with:

**1. Preload the LCP resource:**
```tsx
// Next.js — priority prop preloads image
import Image from 'next/image';

export default function Hero() {
  return (
    <Image
      src="/hero.jpg"
      alt="Hero"
      width={1200}
      height={600}
      priority  // Adds <link rel="preload"> — critical for LCP image
      sizes="100vw"
      quality={85}
    />
  );
}
```

**2. Serve from edge CDN — always.** Static assets behind a CDN reduce LCP by 200–800ms for global users.

**3. Inline critical CSS.** Don't block render with large stylesheets. Tailwind CSS generates minimal CSS by default; ensure unused classes are purged.

**4. Use `font-display: swap`:**
```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/myfont.woff2') format('woff2');
  font-display: swap;  /* Show fallback font immediately, swap when loaded */
}
```

**5. Server-side render the LCP element.** If your LCP is in a Client Component that loads after JS hydration, move it to a Server Component so it's in the initial HTML.

---

## INP (Interaction to Next Paint) Optimization

INP measures every interaction (click, keypress, tap) — the worst 98th percentile. React's reconciliation is the #1 cause of poor INP in React apps.

**1. React Compiler (biggest single win):**
The React Compiler (stable late 2025) automatically memoizes components, reducing unnecessary re-renders by 20–60% with zero code changes. Enable it:
```js
// next.config.js
experimental: { reactCompiler: true }
```

**2. React Server Components reduce client JS:**
Every KB of client JS that executes on interaction blocks the main thread. Moving components to RSC reduces the amount of JS that can delay interaction.

**3. Code-split aggressively:**
```tsx
import dynamic from 'next/dynamic';

// Heavy chart library only loads when needed
const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <Skeleton />,
  ssr: false,  // Skip SSR if not needed for initial paint
});
```

**4. Defer non-critical scripts:**
```tsx
import Script from 'next/script';

// Analytics, chat widgets — don't block interactivity
<Script src="https://analytics.example.com/script.js" strategy="lazyOnload" />
```

**5. Virtual lists for long lists:**
```tsx
import { useVirtualizer } from '@tanstack/react-virtual';

function VirtualList({ items }) {
  const parentRef = useRef(null);
  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 60,
  });
  
  return (
    <div ref={parentRef} style={{ height: 600, overflow: 'auto' }}>
      <div style={{ height: virtualizer.getTotalSize() }}>
        {virtualizer.getVirtualItems().map((item) => (
          <div key={item.key} style={{ transform: `translateY(${item.start}px)` }}>
            {items[item.index].name}
          </div>
        ))}
      </div>
    </div>
  );
}
```

**6. Avoid heavy work on the main thread during interactions:**
```typescript
// Move expensive computation off main thread with Web Workers
// or use scheduler.postTask for non-urgent work
scheduler.postTask(() => {
  expensiveAnalyticsCalculation(data);
}, { priority: 'background' });
```

---

## CLS (Cumulative Layout Shift) Fixes

CLS is caused by elements shifting after initial paint.

**Always set explicit dimensions:**
```tsx
// BAD — image causes layout shift when it loads
<img src="/photo.jpg" alt="Photo" />

// GOOD — dimensions prevent layout shift
<Image src="/photo.jpg" alt="Photo" width={800} height={600} />
```

**Reserve space for async content:**
```tsx
// Skeleton loader — prevents layout shift
function UserCard({ userId }) {
  const { data } = useQuery({ queryKey: ['user', userId], queryFn: fetchUser });
  
  if (!data) return <div className="h-24 w-full animate-pulse bg-gray-200 rounded" />;
  return <div>{data.name}</div>;
}
```

**Font loading CLS:** Use `font-display: optional` if you don't want any fallback flash, or `swap` with fallback metrics configured to match the web font dimensions.

---

## Bundle Analysis & Code Splitting

```bash
# Next.js bundle analyzer
npm install @next/bundle-analyzer
ANALYZE=true npm run build
```

```js
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});
module.exports = withBundleAnalyzer({});
```

**Common bundle optimization moves:**
1. Check `node_modules` in the bundle — date-fns, lodash, etc. can be tree-shaken
2. Replace `import _ from 'lodash'` with `import debounce from 'lodash/debounce'`
3. Use `date-fns` instead of `moment` (modular, tree-shakeable)
4. Use `next/dynamic` for any component >50KB that isn't needed on initial paint
5. Move large libraries to Server Components if they don't need browser APIs

**Route-level code splitting is automatic** in Next.js App Router. Each `page.tsx` creates a separate JS chunk.

---

## React Performance Patterns in 2026

With the React Compiler stable, manual memoization is mostly unnecessary. But these patterns still matter:

**When to still use `useMemo`:**
```tsx
// 1. Effect dependency stability
const filters = useMemo(() => ({ status: 'active', userId }), [userId]);
useEffect(() => { fetchData(filters); }, [filters]); // Stable reference

// 2. Expensive calculations not caught by compiler
const sortedData = useMemo(() =>
  data.slice().sort((a, b) => b.score - a.score),
  [data]
);
```

**When to still use `useCallback`:**
```tsx
// 1. Passing callbacks to heavily optimized child components
const handleSort = useCallback((column: string) => {
  setSortColumn(column);
}, []); // Passes referential equality check in child
```

**The RSC performance pattern (most impactful):**
Move data fetching and heavy computation to Server Components. Client receives rendered HTML + minimal JS.

```tsx
// BAD: Client fetches data, increases bundle, delays interactivity
'use client';
function Dashboard() {
  const { data } = useQuery({ queryFn: fetchDashboardData }); // Fetches after hydration
  return <Charts data={data} />;
}

// GOOD: Server fetches, client only gets interactive parts
async function Dashboard() {  // Server Component
  const data = await fetchDashboardData(); // Fetch on server, zero client JS
  return <Charts data={data} />;
}
```

---

## Image Optimization

```tsx
// Next.js Image — always use this instead of <img>
import Image from 'next/image';

<Image
  src="/product.jpg"
  alt="Product"
  width={400}
  height={300}
  sizes="(max-width: 768px) 100vw, 400px"  // Responsive sizes for srcset
  quality={80}  // Default 75, 80 is sweet spot quality/size
  placeholder="blur"  // Show blurred placeholder while loading
  blurDataURL={blurDataUrl}
/>
```

**For user-uploaded images:** Use Cloudinary or Cloudflare Images for transform-on-the-fly (resize, format conversion, quality optimization) without storing multiple variants.
