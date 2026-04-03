# Supabase Auth + Memory - Detailed Guide

## Authentication Patterns

### Sign Up
```typescript
async function signUp(email: string, password: string) {
  const { data, error } = await supabase.auth.signUp({
    email,
    password,
    options: {
      emailRedirectTo: `${window.location.origin}/auth/callback`
    }
  })

  if (error) throw error
  return data
}
```

### Sign In
```typescript
async function signIn(email: string, password: string) {
  const { data, error } = await supabase.auth.signInWithPassword({
    email,
    password
  })

  if (error) throw error
  return data
}
```

### OAuth (Google, GitHub, etc.)
```typescript
async function signInWithOAuth(provider: 'google' | 'github') {
  const { data, error } = await supabase.auth.signInWithOAuth({
    provider,
    options: {
      redirectTo: `${window.location.origin}/auth/callback`
    }
  })

  if (error) throw error
  return data
}
```

## Supabase MCP Integration

### Setup (.mcp.json)
```json
{
  "mcpServers": {
    "supabase": {
      "url": "https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF",
      "transport": "streamable-http",
      "auth": {
        "type": "oauth"
      }
    }
  }
}
```

### Available MCP Tools
- Create/manage projects
- Design tables & migrations
- Query data with SQL
- Generate TypeScript types
- Manage configurations

## Best Practices

1. **Security First**
   - Always enable RLS on all tables
   - Use service key only on server
   - Validate user ownership in policies

2. **Hybrid Storage**
   - localStorage for offline/instant UX
   - Supabase for cross-device sync
   - Background sync with debouncing

3. **Optimistic UI**
   - Update UI immediately
   - Sync to Supabase in background
   - Handle conflicts gracefully

4. **Real-time Updates**
   - Use subscriptions for collaboration
   - Clean up subscriptions on unmount
   - Debounce rapid updates

5. **Error Handling**
   - Retry failed syncs
   - Show sync status to user
   - Fallback to localStorage

6. **Performance**
   - Index frequently queried columns
   - Use JSONB for flexible metadata
   - Implement pagination for large datasets

## Common Patterns

### Multi-tenant SaaS
```sql
-- Add tenant isolation
CREATE TABLE organizations (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE TABLE organization_members (
  organization_id UUID REFERENCES organizations(id),
  user_id UUID REFERENCES auth.users(id),
  role TEXT NOT NULL CHECK (role IN ('owner', 'admin', 'member')),
  PRIMARY KEY (organization_id, user_id)
);

-- Update RLS policies for organization isolation
CREATE POLICY "Organization members can view conversations"
ON conversations FOR SELECT
USING (
  user_id IN (
    SELECT user_id FROM organization_members
    WHERE organization_id = (
      SELECT organization_id FROM organization_members
      WHERE user_id = auth.uid()
    )
  )
);
```

## Resources

- [Supabase Docs](https://supabase.com/docs)
- [Supabase MCP Server](https://supabase.com/features/mcp-server)
- [RLS Policies Guide](https://supabase.com/docs/guides/auth/row-level-security)
- [Real-time Guide](https://supabase.com/docs/guides/realtime)
