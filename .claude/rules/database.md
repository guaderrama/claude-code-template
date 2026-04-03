---
paths:
  - "supabase/**"
  - "src/shared/lib/supabase*"
  - "src/**/services/**"
---

# Database Rules (Supabase/PostgreSQL)

## Row Level Security (RLS)
- EVERY table must have RLS enabled — no exceptions
- Policies use `auth.uid()` for user-scoped access
- Test policies: verify users cannot access other users' data

## Migrations
- One concern per migration file
- Always include rollback (DOWN) logic
- Never modify existing migration files — create new ones
- Name format: `YYYYMMDDHHMMSS_descriptive_name.sql`

## Query Performance
- Add indexes on columns used in WHERE, JOIN, ORDER BY
- Use `select()` with specific columns, never `select('*')` in production
- Paginate with `.range()` for lists — never fetch unbounded results
- Use `.single()` when expecting exactly one row

## Connection Management
- Use Supabase client from `src/shared/lib/supabase.ts` (singleton)
- Server components use `createServerClient`, client components use `createBrowserClient`
- Never instantiate Supabase clients inside loops or renders

## Schema Design
- UUIDs for primary keys (`gen_random_uuid()`)
- `created_at` and `updated_at` timestamps on every table
- Soft deletes with `deleted_at` when data retention matters
- Foreign keys with appropriate ON DELETE (CASCADE/SET NULL)

## Type Safety
- Regenerate types after schema changes: `npx supabase gen types typescript --local`
- Import database types from `types/supabase.ts`
