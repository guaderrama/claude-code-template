---
name: nextjs-16-complete-guide
description: Complete guide to Next.js 16 features, breaking changes, and migration from v15. Use when building new Next.js projects or upgrading existing ones to leverage Turbopack, Cache Components, and latest performance optimizations.
license: MIT
model: sonnet
effort: medium
---

# Next.js 16 Complete Guide

## Purpose
Comprehensive reference for Next.js 16's revolutionary features: Cache Components with "use cache", stable Turbopack as default bundler, proxy.ts architecture, DevTools MCP integration, and React Compiler support.

## When to Use
- Starting new Next.js projects (use 16 from day one)
- Migrating from Next.js 15 to 16
- Understanding Cache Components and Partial Pre-Rendering (PPR)
- Configuring Turbopack for optimal performance
- Migrating middleware.ts to proxy.ts
- Leveraging AI-assisted debugging with DevTools MCP
- Setting up React Compiler for automatic memoization

## What Changed: Next.js 15 → 16

### The Big Picture
Next.js 15 was **transition phase** - async APIs, experimental Turbopack, changing cache defaults.
Next.js 16 is **the payoff** - everything becomes stable, fast, and production-ready.

### Key Differences

| Feature | Next.js 15 | Next.js 16 |
|---------|-----------|-----------|
| **Bundler** | Webpack (default), Turbopack (opt-in beta) | Turbopack (default, stable) |
| **Caching** | Implicit, confusing defaults | Explicit with "use cache" |
| **Network Layer** | middleware.ts (edge runtime) | proxy.ts (Node.js runtime) |
| **DevTools** | Basic error messages | MCP integration for AI debugging |
| **React Compiler** | Experimental | Stable, production-ready |
| **Performance** | Baseline | 2-5× faster builds, 10× faster Fast Refresh |

---

## 🚀 Core Features (The 20% That Delivers 80%)

### 1. Cache Components + "use cache"

**The Problem in Next.js 15:**
- Implicit caching was "magic" - hard to predict what cached
- Switching between static/dynamic was unclear
- Performance optimization felt like guesswork

**The Solution in Next.js 16:**
```typescript
// Enable in next.config.ts
const nextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

**Usage Pattern:**
```typescript
// app/dashboard/page.tsx
import { Suspense } from 'react';

// This component caches its output
async function UserMetrics() {
  'use cache'; // 🎯 Explicit caching

  const metrics = await fetchMetrics(); // Cached result

  return <MetricsCard data={metrics} />;
}

// This stays dynamic
async function LiveBalance() {
  const balance = await fetchBalance(); // Always fresh
  return <BalanceWidget balance={balance} />;
}

export default function Dashboard() {
  return (
    <div>
      <Suspense fallback={<LoadingMetrics />}>
        <UserMetrics /> {/* Cached, instant load */}
      </Suspense>

      <LiveBalance /> {/* Dynamic, real-time */}
    </div>
  );
}
```

**Why This Matters:**
- **Instant navigation** - Cached parts load immediately
- **Selective freshness** - Only dynamic parts fetch on demand
- **Predictable behavior** - You control what caches
- **SaaS dashboards** - Perfect for panels with mixed static/dynamic content

**Cache Granularity:**
```typescript
// Cache entire page
export default async function Page() {
  'use cache';
  return <PageContent />;
}

// Cache individual component
async function ExpensiveWidget() {
  'use cache';
  return <Chart data={await getData()} />;
}

// Cache function result
async function getStats() {
  'use cache';
  return await database.query('...');
}
```

---

### 2. Turbopack: Default Bundler (Stable)

**Performance Numbers (Official Vercel Benchmarks):**
- **2-5× faster production builds**
- **Up to 10× faster Fast Refresh** in development
- **File system caching** - Even faster restarts on large projects

**No Configuration Needed:**
```typescript
// next.config.ts
// Turbopack is now default - no config required!
```

**Opt-out (if needed):**
```bash
# Use Webpack instead
next build --webpack
```

**File System Caching (Beta):**
```typescript
// next.config.ts
const nextConfig = {
  experimental: {
    turbopackFileSystemCacheForDev: true, // Faster restarts
  },
};
```

**Why This Matters:**
- **Faster feedback loop** - See changes instantly (10× faster)
- **Shorter CI/CD times** - 2-5× faster production builds
- **Better DX** - Less waiting, more shipping
- **Large projects** - Scales better than Webpack

**What You Notice:**
```bash
# Before (Webpack)
✓ Compiled in 4.2s

# After (Turbopack)
✓ Compiled in 0.4s  # 10× faster
```

---

### 3. proxy.ts Replaces middleware.ts

**The Change:**
```bash
# Old (Next.js 15)
middleware.ts  # Edge runtime, confusing

# New (Next.js 16)
proxy.ts       # Node.js runtime, explicit
```

**Migration Example:**
```typescript
// OLD: middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  return NextResponse.redirect(new URL('/home', request.url));
}

export const config = {
  matcher: '/about/:path*',
};
```

```typescript
// NEW: proxy.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export default function proxy(request: NextRequest) {  // Changed function name
  return NextResponse.redirect(new URL('/home', request.url));
}

export const config = {
  matcher: '/about/:path*',
};
```

**Key Changes:**
1. Rename file: `middleware.ts` → `proxy.ts`
2. Rename export: `export function middleware` → `export default function proxy`
3. Runtime: Runs on Node.js (not edge)

**Why This Matters:**
- **Clearer boundary** - "Proxy" = network entry point
- **Predictable runtime** - Always Node.js, no edge ambiguity
- **Better debugging** - Standard Node.js environment

---

### 4. DevTools MCP (AI-Assisted Debugging)

**What It Does:**
Next.js 16 integrates Model Context Protocol (MCP) so AI agents can:
- Read unified browser + server logs
- Understand Next.js routing and caching
- Access error stack traces automatically
- Provide page-aware debugging context

**Why This Matters:**
- **AI copilots** can debug your Next.js app natively
- **Faster debugging** - AI understands framework internals
- **Better DX** - Agent sees what you see (and more)

**Use Case:**
```
You: "Why is this page not caching?"
AI Agent (with MCP):
  - Reads server logs
  - Sees route configuration
  - Checks cache headers
  - Knows Next.js 16 caching rules
  → "You're missing 'use cache' directive in your component"
```

**Integration:**
Works automatically with Claude Code, Cursor, and other MCP-compatible tools.

---

### 5. React Compiler (Stable)

**What It Does:**
Automatically memoizes components - no more manual `useMemo`, `useCallback`, `React.memo`.

**Setup:**
```bash
npm install babel-plugin-react-compiler@latest
```

```typescript
// next.config.ts
const nextConfig = {
  reactCompiler: true,  // Moved from experimental to stable
};
```

**Before (Manual Optimization):**
```typescript
// You had to do this everywhere
const MemoizedComponent = React.memo(function Component({ data }) {
  const processed = useMemo(() => processData(data), [data]);
  const handleClick = useCallback(() => {
    console.log(processed);
  }, [processed]);

  return <div onClick={handleClick}>{processed}</div>;
});
```

**After (React Compiler Does It):**
```typescript
// Just write code, compiler optimizes automatically
function Component({ data }) {
  const processed = processData(data);  // Auto-memoized
  const handleClick = () => {            // Auto-memoized
    console.log(processed);
  };

  return <div onClick={handleClick}>{processed}</div>;
}
```

**Why This Matters:**
- **Less boilerplate** - Write cleaner code
- **Better performance** - Compiler is smarter than manual optimization
- **No bugs** - Compiler never forgets dependencies

---

## 🔥 Breaking Changes (15 → 16)

### Required Changes

#### 1. Node.js Version
```bash
# Minimum: Node.js 20.9+
# Next.js 15: Node.js 18 worked
# Next.js 16: Node.js 18 no longer supported
```

#### 2. Async params & searchParams
```typescript
// ❌ OLD (Next.js 15)
export default function Page({ params, searchParams }) {
  const id = params.id;  // Synchronous
}

// ✅ NEW (Next.js 16)
export default async function Page({ params, searchParams }) {
  const { id } = await params;  // Must await
  const { query } = await searchParams;
}
```

#### 3. Async cookies(), headers(), draftMode()
```typescript
// ❌ OLD
import { cookies } from 'next/headers';

export function MyComponent() {
  const token = cookies().get('token');  // Synchronous
}

// ✅ NEW
import { cookies } from 'next/headers';

export async function MyComponent() {
  const cookieStore = await cookies();  // Must await
  const token = cookieStore.get('token');
}
```

#### 4. revalidateTag() Now Requires cacheLife Profile
```typescript
// ❌ OLD
revalidateTag('posts');

// ✅ NEW
revalidateTag('posts', 'max');     // Options: 'max', 'hours', 'days'
```

#### 5. middleware.ts → proxy.ts
```bash
# Required migration
mv middleware.ts proxy.ts

# Update function name
export default function proxy(request) { ... }
```

### Removed Features

- **AMP support** - Completely removed
- **`next lint` command** - Removed
- **Node.js 18** - No longer supported
- **TypeScript <5.1** - No longer supported

---

## 📦 Migration Guide (Step-by-Step)

### 1. Pre-Migration Checklist

```bash
# Check current versions
node --version    # Should be 20.9+
npm list next     # Current Next.js version
npm list react    # React version
```

### 2. Automated Migration

```bash
# Upgrade to latest Next.js 16
npm install next@latest react@latest react-dom@latest

# Run codemod (handles most changes automatically)
npx @next/codemod@canary upgrade latest
```

### 3. Manual Changes

**Update next.config:**
```typescript
// next.config.ts
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  // Enable Cache Components
  cacheComponents: true,

  // Enable React Compiler
  reactCompiler: true,

  // Optional: File system caching
  experimental: {
    turbopackFileSystemCacheForDev: true,
  },
};

export default nextConfig;
```

**Rename middleware:**
```bash
# If you have middleware.ts
git mv middleware.ts proxy.ts

# Update function name in proxy.ts
export default function proxy(request: NextRequest) {
  // Your logic
}
```

**Update async APIs:**
```typescript
// Search codebase for:
// - params.
// - searchParams.
// - cookies()
// - headers()
// - draftMode()

// Add await before each
```

### 4. Test Everything

```bash
# Development
npm run dev  # Should use Turbopack automatically

# Production build
npm run build  # Should be 2-5× faster

# Run tests
npm test
```

### 5. Deploy

```bash
# Verify Node.js version in production
# Deploy to Vercel/platform
# Monitor for errors in first 24h
```

---

## Extended Reference

For detailed migration guides, code examples, and advanced patterns, see `references/detailed-guide.md`.
