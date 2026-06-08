# Authentication & Authorization (2025–2026)

## Auth Provider Selection

| Provider | Price | Best For | Passkeys | RBAC | Multi-Tenancy |
|---|---|---|---|---|---|
| **Clerk** | ~$100/mo at 5K MAUs | B2B SaaS, orgs/teams | Yes (built-in) | Yes | Yes (Organizations) |
| **Auth.js v5** | Free (self-hosted) | Self-hosted apps, flexible | Partial | DIY | DIY |
| **Better Auth** | Free (self-hosted) | Feature-rich self-hosted | Yes | Yes (plugin) | Yes (plugin) |
| **Supabase Auth** | Free tier → $25/mo | Apps already on Supabase | Yes | Via RLS | Via schema |
| **Lucia** | Free (library) | Full control, custom flows | DIY | DIY | DIY |
| **WorkOS** | Enterprise pricing | Enterprise SSO, SOC2 | Yes | Yes | Yes |

**Quick rule:**
- Building B2B SaaS with orgs/teams → **Clerk**
- Budget-constrained, self-hosted control → **Better Auth** (replaces NextAuth as the modern self-hosted pick)
- Already on Supabase → **Supabase Auth**
- Need enterprise SSO (SAML, SCIM) → **WorkOS**

---

## Clerk (The Managed Option)

Clerk handles auth UI, session management, user management, organizations, and RBAC out of the box.

```tsx
// Next.js App Router + Clerk
// middleware.ts
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server';

const isProtectedRoute = createRouteMatcher(['/dashboard(.*)']);

export default clerkMiddleware((auth, req) => {
  if (isProtectedRoute(req)) auth().protect();
});

// Server Component — access session directly
import { auth, currentUser } from '@clerk/nextjs/server';

export default async function DashboardPage() {
  const { userId, orgId, orgRole } = auth();
  const user = await currentUser();
  return <div>Hello {user?.firstName}</div>;
}

// RBAC check
if (orgRole === 'org:admin') { /* admin-only logic */ }
```

**Clerk Organizations = multi-tenancy built-in:**
- Users belong to organizations (tenants)
- Each org has members with roles
- Per-org metadata, billing, settings
- Works with passkeys across all orgs

---

## Auth.js v5 (NextAuth)

Auth.js v5 (stable 2025) is the renamed NextAuth, rewritten for App Router compatibility.

```typescript
// auth.ts
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";
import { DrizzleAdapter } from "@auth/drizzle-adapter";

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: DrizzleAdapter(db),
  providers: [
    GitHub,
    // Magic link
    Resend({ from: "noreply@yourapp.com" }),
  ],
  callbacks: {
    authorized({ auth, request: { nextUrl } }) {
      const isLoggedIn = !!auth?.user;
      const isOnDashboard = nextUrl.pathname.startsWith('/dashboard');
      if (isOnDashboard && !isLoggedIn) return false;
      return true;
    },
    session({ session, user }) {
      // Add custom fields to session
      session.user.role = user.role;
      return session;
    },
  },
});

// Route handler
// app/api/auth/[...nextauth]/route.ts
export { handlers as GET, handlers as POST };

// Server Component usage
import { auth } from "@/auth";

export default async function Page() {
  const session = await auth();
  if (!session) redirect('/login');
  return <div>Hello {session.user.name}</div>;
}
```

**Auth.js vs Clerk trade-offs:**
- Auth.js: MIT licensed, self-hosted, full control, no vendor lock-in, $0 cost
- Auth.js: missing built-in 2FA, passkeys (partial), org management
- Clerk: $0 to start but expensive at scale, built-in everything, less control

---

## JWT vs Session Cookies

**Session Cookies (recommended for most apps):**
- Server stores session data (in database or Redis)
- Client gets a session ID cookie (httpOnly, Secure, SameSite=Lax)
- More secure: server can invalidate sessions immediately
- Slightly more infrastructure (session store)

**JWTs (for specific use cases):**
- Stateless: no server-side session store needed
- Best for: microservices auth, mobile apps, API-to-API communication
- Weakness: cannot invalidate before expiry (use short expiry + refresh tokens)
- NEVER store in localStorage — XSS vulnerability. Store in httpOnly cookies.

```typescript
// JWT best practices
const token = await jose.SignJWT({ sub: userId, role: user.role })
  .setProtectedHeader({ alg: 'ES256' })  // Asymmetric, not HS256
  .setExpirationTime('15m')               // Short-lived
  .sign(privateKey);

// Refresh token pattern
// Access token: 15 minutes, stored in memory or httpOnly cookie
// Refresh token: 7 days, stored in httpOnly cookie, rotated on use
```

---

## Passkeys (WebAuthn)

Passkeys are phishing-resistant, passwordless authentication using device biometrics (Face ID, Touch ID, Windows Hello). Over 15 billion accounts now support them (FIDO Alliance, 2025).

```typescript
// Clerk handles passkey registration/authentication entirely
// Custom WebAuthn with @simplewebauthn/server

import { generateRegistrationOptions, verifyRegistrationResponse } from '@simplewebauthn/server';

// Registration flow
const options = await generateRegistrationOptions({
  rpName: 'My App',
  rpID: 'myapp.com',
  userID: userId,
  userName: userEmail,
  attestationType: 'none',
  authenticatorSelection: {
    residentKey: 'required',    // Passkey (discoverable credential)
    userVerification: 'required',
  },
});

// Store challenge, return options to client
// Client calls navigator.credentials.create(options)
// Server verifies with verifyRegistrationResponse()
```

---

## RBAC vs ABAC

**RBAC (Role-Based Access Control):**
- Users have roles, roles have permissions
- Simple, performant, easy to audit
- Right choice for most SaaS apps

```typescript
// Middleware-level RBAC check
const ROLE_PERMISSIONS = {
  admin: ['read', 'write', 'delete', 'manage_users'],
  editor: ['read', 'write'],
  viewer: ['read'],
} as const;

function can(userRole: keyof typeof ROLE_PERMISSIONS, action: string) {
  return ROLE_PERMISSIONS[userRole]?.includes(action) ?? false;
}
```

**ABAC (Attribute-Based Access Control):**
- Access based on attributes (user department, resource owner, time of day)
- More flexible, more complex
- Use when RBAC permissions become unwieldy (e.g., "user can edit their own posts but not others'")

**Supabase Row Level Security (RLS) — ABAC at the database level:**
```sql
-- Users can only read their own data
CREATE POLICY "users_own_data" ON users
  FOR ALL USING (auth.uid() = id);

-- Org members can read org resources
CREATE POLICY "org_members_read" ON documents
  FOR SELECT USING (
    org_id IN (
      SELECT org_id FROM org_members WHERE user_id = auth.uid()
    )
  );
```

---

## Multi-Tenancy Patterns

**Schema-per-tenant:** Each tenant has their own PostgreSQL schema. Strong isolation, complex migrations. Use for regulated industries.

**Row-level isolation (most common):** All tenants in same tables, filtered by `org_id` + RLS. Simplest to implement, works great with Supabase RLS.

```typescript
// Every query is automatically scoped to the current org
// Using Drizzle + middleware to inject org_id
const db = drizzle(client);

// Create a scoped DB helper
function scopedDb(orgId: string) {
  return {
    findDocuments: () =>
      db.select().from(documents).where(eq(documents.orgId, orgId)),
    createDocument: (data: NewDocument) =>
      db.insert(documents).values({ ...data, orgId }).returning(),
  };
}
```

**Database-per-tenant:** Strongest isolation, most expensive. Use for enterprise customers with strict data residency requirements.
