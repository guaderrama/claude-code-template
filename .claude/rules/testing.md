---
paths:
  - "src/**/*.test.*"
  - "src/**/*.spec.*"
  - "src/**/__tests__/**"
  - "tests/**"
  - "e2e/**"
---

# Testing Rules

## TDD Workflow (mandatory)
1. RED: Write a failing test that describes expected behavior
2. GREEN: Write minimum code to make it pass
3. REFACTOR: Clean up while keeping tests green

## AAA Pattern
```typescript
// Arrange — set up test data and dependencies
const user = createMockUser({ role: 'admin' });

// Act — execute the behavior under test
const result = await getPermissions(user);

// Assert — verify the expected outcome
expect(result).toContain('manage_users');
```

## Coverage Targets
- Minimum 80% overall coverage
- 100% for business logic and utilities
- Focus on behavior, not implementation details

## What to Test
- **Unit tests**: Pure functions, hooks, utilities, store actions
- **Integration tests**: API routes, database queries, service layers
- **Component tests**: User interactions, conditional rendering, form validation
- **E2E tests (Playwright)**: Critical user flows (auth, checkout, etc.)

## What NOT to Test
- Third-party library internals
- Static UI without logic (pure presentational)
- TypeScript types (the compiler handles this)

## Test Naming
- Describe blocks: component/function name
- It blocks: `should [expected behavior] when [condition]`
- Example: `it('should show error message when email is invalid')`

## Mocking Guidelines
- Mock external services (APIs, Supabase), NOT internal modules
- Use `jest.spyOn` over `jest.mock` when possible
- Reset mocks in `afterEach` to prevent test pollution
