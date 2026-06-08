# State Management & Data Fetching (2025–2026)

## The Core Principle: Separate Server State from Client State

The single most important state management insight in 2026:

**Server state** (data from your API/DB) → TanStack Query  
**Client state** (UI state, user preferences, in-memory interactions) → Zustand or Jotai  
**Form state** → React Hook Form + Zod  
**URL state** → nuqs (type-safe URL search params)

If you're using Redux, Zustand, or Jotai to store data fetched from a server, you're doing extra work. TanStack Query handles caching, deduplication, background refetching, stale-while-revalidate, and optimistic updates — things a general state manager can't do automatically.

---

## TanStack Query v5 (Server State)

TanStack Query is the undisputed standard for server state in React as of 2026. Version 5 (released late 2023) simplified the API significantly.

```tsx
// Basic fetch with caching and background refetch
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

function ProductList() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['products'],
    queryFn: () => fetch('/api/products').then(r => r.json()),
    staleTime: 5 * 60 * 1000,  // Consider fresh for 5 minutes
  });
  
  if (isLoading) return <Skeleton />;
  if (error) return <ErrorBoundary error={error} />;
  return <ul>{data.map(p => <ProductRow key={p.id} product={p} />)}</ul>;
}

// Optimistic mutation
function useUpdateProduct() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (update) => fetch(`/api/products/${update.id}`, {
      method: 'PATCH',
      body: JSON.stringify(update),
    }),
    onMutate: async (update) => {
      await queryClient.cancelQueries({ queryKey: ['products'] });
      const previous = queryClient.getQueryData(['products']);
      
      // Optimistically update the cache
      queryClient.setQueryData(['products'], (old) =>
        old.map(p => p.id === update.id ? { ...p, ...update } : p)
      );
      return { previous };
    },
    onError: (err, update, context) => {
      // Roll back on error
      queryClient.setQueryData(['products'], context.previous);
    },
    onSettled: () => queryClient.invalidateQueries({ queryKey: ['products'] }),
  });
}
```

**Key TanStack Query concepts:**
- `staleTime`: How long data is considered fresh (no background refetch). Default: 0 (always refetch on mount).
- `gcTime` (formerly `cacheTime`): How long to keep unused data in cache. Default: 5 minutes.
- `queryKey`: Array that uniquely identifies the query. Include all variables that affect the result: `['products', { page, filter }]`.
- `invalidateQueries`: Tell TanStack Query that cache is stale after a mutation.

**Stale-while-revalidate pattern:**
```tsx
const { data } = useQuery({
  queryKey: ['user-profile'],
  queryFn: fetchProfile,
  staleTime: 60_000,     // Show cached data for 1 minute without refetching
  gcTime: 5 * 60_000,   // Keep in cache for 5 minutes after unmount
});
// User sees instant data from cache; background refetch updates silently
```

**In Next.js App Router (RSC + TanStack Query):**
```tsx
// Server Component — prefetch on server
import { dehydrate, HydrationBoundary, QueryClient } from '@tanstack/react-query';

export default async function Page() {
  const queryClient = new QueryClient();
  await queryClient.prefetchQuery({
    queryKey: ['products'],
    queryFn: fetchProducts,
  });
  
  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <ProductList />  {/* Client Component with useQuery — gets prefetched data */}
    </HydrationBoundary>
  );
}
```

---

## Zustand (Client State)

Zustand is the default choice for client-side global state. It's small (~1KB), requires no Provider wrapping, and avoids Context re-render problems.

```tsx
import { create } from 'zustand';
import { devtools, persist } from 'zustand/middleware';

interface CartStore {
  items: CartItem[];
  addItem: (item: CartItem) => void;
  removeItem: (id: string) => void;
  total: number;
}

const useCartStore = create<CartStore>()(
  devtools(
    persist(
      (set, get) => ({
        items: [],
        addItem: (item) => set((state) => ({ items: [...state.items, item] })),
        removeItem: (id) => set((state) => ({
          items: state.items.filter(i => i.id !== id)
        })),
        get total() {
          return get().items.reduce((sum, i) => sum + i.price, 0);
        },
      }),
      { name: 'cart-storage' }  // Persists to localStorage
    )
  )
);

// Subscribe to only what you need (prevents unnecessary re-renders)
function CartCount() {
  const count = useCartStore((state) => state.items.length);
  return <span>{count}</span>;
}
```

**Zustand vs Context API:**
- Context API re-renders every consumer on any state change
- Zustand components only re-render when their specific selector changes
- Rule: if more than 2 components share state across the tree, use Zustand over Context

---

## Jotai (Atomic Client State)

Jotai is better than Zustand when state is highly granular — many independent small atoms rather than one big store.

```tsx
import { atom, useAtom, useAtomValue, useSetAtom } from 'jotai';

const countAtom = atom(0);
const doubledAtom = atom((get) => get(countAtom) * 2);

// Derived async atom
const userAtom = atom(async (get) => {
  const id = get(userIdAtom);
  return fetch(`/api/users/${id}`).then(r => r.json());
});

function Counter() {
  const [count, setCount] = useAtom(countAtom);
  const doubled = useAtomValue(doubledAtom);
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <span>Doubled: {doubled}</span>
    </div>
  );
}
```

**Choose Jotai over Zustand when:**
- State is naturally decomposable into independent atoms
- You need derived state that automatically recomputes
- You want React Suspense integration for async state

---

## When You Don't Need a State Manager

In 2026, with React Server Components and TanStack Query, many apps need minimal client state:

1. **Data from server** → TanStack Query or RSC async components
2. **Form state** → React Hook Form (uncontrolled) + Zod
3. **URL-driven state** (filters, pagination, tabs) → `nuqs` library (type-safe URL params)
4. **Component-local state** → `useState` / `useReducer`
5. **Cross-component UI state** (modal open/close, sidebar) → Zustand atom or simple Context

Only reach for a global state manager when state truly needs to be:
- Shared across many unrelated components
- Persisted across navigation
- Complex enough to warrant selectors and actions

**Smell test:** If you're storing `users: User[]` in Zustand and fetching it with `useEffect`, you're using Zustand as a poor man's cache. Switch to TanStack Query.

---

## Redux Toolkit in 2026

Redux Toolkit (RTK) + RTK Query is still valid for:
- Large enterprise codebases with existing Redux
- Teams that want strict action/reducer patterns for auditing
- Complex state machines with many interdependencies

RTK Query is a capable alternative to TanStack Query with tighter Redux integration. For new projects without a Redux legacy, Zustand + TanStack Query is simpler and faster to ship.
