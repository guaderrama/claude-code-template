## Best Practices

### 1. Start New Projects with Next.js 16
```bash
npx create-next-app@latest my-app
# Automatically uses Next.js 16, Turbopack, TypeScript, Tailwind
```

### 2. Use "use cache" Strategically
```typescript
// Good: Cache expensive, infrequent-changing data
async function MonthlyReport() {
  'use cache';
  return await generateReport();
}

// Bad: Don't cache user-specific real-time data
async function CurrentBalance() {
  'use cache';  // Wrong! This should be fresh
  return await getUserBalance();
}
```

### 3. Monitor Build Performance
```bash
# Next.js 16 shows detailed timing
npm run build

# Example output:
Route (app)                              Size     First Load JS
┌ ○ /                                   142 B          87.2 kB
├ ○ /_not-found                         142 B          87.2 kB
└ ○ /dashboard                          1.23 kB        88.4 kB

Build time: 14.2s  # Was 57s in Next.js 15 with Webpack
```

### 4. Leverage DevTools MCP
- Use Claude Code / Cursor with MCP support
- Let AI agent read Next.js internals
- Ask: "Why is my page slow?" - Agent sees server logs

### 5. Enable React Compiler for Large Apps
```typescript
// Only if you have performance issues
reactCompiler: true
```

---

## When to Upgrade (Decision Matrix)

### Upgrade Now If:
- Starting new project (use 16 from day one)
- Small/medium codebase (<50 routes)
- Using Vercel (tested platform)
- Want 2-5x faster builds
- Need instant navigation (Cache Components)

### Wait 1-2 Weeks If:
- Large production app (>100 routes)
- Heavy middleware.ts usage (needs proxy.ts migration)
- Custom Webpack config
- Third-party libs not yet compatible
- Risk-averse deployment policy

### Don't Upgrade Yet If:
- Using AMP pages (removed in 16)
- Stuck on Node.js 18 (can't upgrade)
- Custom build pipelines (need adapter testing)
- Experimental features in production

---

## Resources

- [Next.js 16 Release Notes](https://nextjs.org/blog/next-16)
- [Upgrade Guide](https://nextjs.org/docs/app/guides/upgrading/version-16)
- [Turbopack Documentation](https://nextjs.org/docs/architecture/turbopack)
- [Cache Components Guide](https://nextjs.org/docs/app/building-your-application/caching)
- [Codemod CLI](https://nextjs.org/docs/app/guides/upgrading/codemods)

---

## Quick Reference

### Installation
```bash
npm install next@latest react@latest react-dom@latest
```

### Enable Features
```typescript
// next.config.ts
const nextConfig = {
  cacheComponents: true,           // Cache Components
  reactCompiler: true,             // React Compiler
  experimental: {
    turbopackFileSystemCacheForDev: true,  // Faster dev restarts
  },
};
```

### Migration Checklist
- [ ] Node.js 20.9+ installed
- [ ] `npm install next@latest`
- [ ] Run `npx @next/codemod@canary upgrade latest`
- [ ] Rename `middleware.ts` -> `proxy.ts`
- [ ] Add `await` to params/searchParams/cookies/headers
- [ ] Update `revalidateTag()` calls with cacheLife
- [ ] Test build: `npm run build`
- [ ] Test dev: `npm run dev`
- [ ] Deploy to staging first

---

**Summary:** Next.js 16 delivers on the promises of 15 - everything is faster, more explicit, and production-ready. Turbopack is default, caching is predictable, and AI tooling is native. This is the version to build modern SaaS applications on in 2025.
