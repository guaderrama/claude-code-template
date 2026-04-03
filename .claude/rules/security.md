---
paths:
  - "src/app/api/**"
  - "src/**/services/**"
  - "src/shared/lib/**"
  - "middleware.*"
---

# Security Rules

## Input Validation
- Validate ALL external input with Zod schemas at API boundaries
- Never trust client-side validation alone — always validate server-side
- Sanitize user input before database queries or HTML rendering

## Authentication & Authorization
- Verify auth on every API route: `const { data: { user } } = await supabase.auth.getUser()`
- Check permissions before data access, not just authentication
- Use middleware for route-level protection patterns

## OWASP Top 10 Prevention
- **Injection**: Use parameterized queries (Supabase handles this) — never string concatenation
- **XSS**: React escapes by default; never use `dangerouslySetInnerHTML` with user content
- **CSRF**: Use SameSite cookies and verify Origin headers
- **Broken Access Control**: RLS policies + server-side auth checks

## Secrets Management
- All secrets in `.env.local` (gitignored) — NEVER in source code
- Use `NEXT_PUBLIC_` prefix ONLY for truly public values
- Server-only secrets accessed only in API routes / server components

## Data Protection
- Hash passwords (Supabase Auth handles this)
- Encrypt sensitive fields at rest when required
- Log user actions for audit trail on sensitive operations
- Never log passwords, tokens, or PII

## API Security
- Rate limiting on authentication endpoints
- Return generic error messages to clients (no stack traces)
- Use HTTPS in production — enforce with HSTS headers
