---
name: refactor-smells
description: Detect 10 specific code smells (long functions >50 lines, files >500 lines, deep nesting >3, parameter lists >3, boolean flags, magic numbers, primitive obsession) and apply concrete refactor recipes. Runs AFTER tests pass. Differs from built-in simplify by providing a fixed taxonomy with before/after recipes.
license: MIT
model: sonnet
effort: medium
---

# Refactor Smells (Code Simplification)

## Purpose
Complex code = more bugs. This skill identifies specific code smells and provides concrete refactor recipes. Runs AFTER tests pass, BEFORE merging.

## When to Use
- After GREEN phase of TDD (refactor step)
- Before opening a PR
- When user says "simplify", "refactorizar", "reduce complexity", "huele mal"
- When CLAUDE.md limits are exceeded (file >500 lines, function >50)

## Code Smells (detect these)

### 1. Long Function (>50 lines)
**Detect:** Function body exceeds 50 lines.
**Why bad:** Can't hold in head; hides bugs in the middle.
**Fix:** Extract sub-functions with intention-revealing names.

```typescript
// BAD — 80 lines of payment processing
async function processPayment(order) { /* ... 80 lines ... */ }

// GOOD — orchestrator + focused helpers
async function processPayment(order) {
  const validated = validateOrder(order);
  const charge = await chargeCard(validated);
  const receipt = await recordPayment(charge);
  await notifyUser(receipt);
  return receipt;
}
```

### 2. Large Class/Module (>500 lines)
**Detect:** Single file exceeds 500 LOC (excluding imports/types) — matches CLAUDE.md hard limit. Sub-300 is an early-warning heuristic for classes with >1 responsibility.
**Why bad:** God object; high cohesion failure.
**Fix:** Split by responsibility (data, logic, I/O).

### 3. Long Parameter List (>3 params)
**Detect:** Function signature has 4+ parameters.
**Why bad:** Hard to call correctly; order errors.
**Fix:** Introduce parameter object.

```typescript
// BAD
createUser(name, email, role, tenantId, isActive, tier)

// GOOD
createUser({ name, email, role, tenantId, isActive, tier })
```

### 4. Deep Nesting (>3 levels)
**Detect:** `if {if {if {if {...}}}}` or equivalent.
**Why bad:** Cognitive load; bugs hide in branches.
**Fix:** Early returns (guard clauses) or extract.

```typescript
// BAD
function save(user) {
  if (user) {
    if (user.email) {
      if (isValid(user.email)) {
        if (!exists(user.email)) {
          db.insert(user);
        }
      }
    }
  }
}

// GOOD
function save(user) {
  if (!user) return;
  if (!user.email) throw new Error('email required');
  if (!isValid(user.email)) throw new Error('invalid email');
  if (exists(user.email)) throw new Error('duplicate');
  db.insert(user);
}
```

### 5. Boolean Flag Parameter
**Detect:** `fn(x, true)` — what does `true` mean at the call site?
**Fix:** Split into two named functions.

```typescript
// BAD
renderUser(user, true)  // what's true?

// GOOD
renderUserAsAdmin(user)
renderUserAsGuest(user)
```

### 6. Magic Numbers/Strings
**Detect:** Unexplained literals in logic (e.g., `if (retries > 3)`).
**Fix:** Named constants with explanatory names.

```typescript
// BAD
if (retries > 3) abort()

// GOOD
const MAX_RETRIES = 3;  // Stripe rate limit recovery
if (retries > MAX_RETRIES) abort();
```

### 7. Duplicated Logic (Rule of 3)
**Detect:** Same logic appears 3+ times with minor variations.
**Fix:** Extract to shared function. (But don't abstract prematurely — 2 is OK.)

### 8. Primitive Obsession
**Detect:** `string` used for email, ID, URL, date interchangeably.
**Fix:** Branded types or value objects.

```typescript
// BAD
function send(email: string, userId: string)  // swap = silent bug

// GOOD
type Email = string & { __brand: 'Email' }
type UserId = string & { __brand: 'UserId' }
function send(email: Email, userId: UserId)
```

### 9. Dead Code
**Detect:** Unreferenced exports, commented-out blocks, unused imports.
**Fix:** Delete. Git has history.

### 10. Conditional Complexity
**Detect:** `if (a && b || c && !d && e)` — unreadable.
**Fix:** Extract to named predicate function.

```typescript
// BAD
if (user.role === 'admin' && !user.suspended && order.amount > 0 && order.status === 'pending')

// GOOD
if (canAdminApproveOrder(user, order))
```

## Workflow

1. **Scan the diff** (or changed files) against all 10 smells
2. **List findings** with file:line and smell name
3. **Prioritize:** Size-based smells (#1, #2) first; they have highest bug ratio
4. **Propose refactor** for each with before/after code
5. **Verify tests still pass** after each refactor (never batch)

## Output Format

```
## Simplification Report

### Smells Detected
1. [Long Function] src/payment.ts:processPayment (82 lines)
2. [Deep Nesting] src/auth.ts:validateSession (4 levels)
3. [Magic Number] src/api/route.ts:15 — `86400` should be `SECONDS_PER_DAY`
4. [Boolean Flag] src/email.ts:send(msg, true) — split into sendUrgent/sendNormal

### Refactor Plan
<for each, show before/after diff>

### Not Refactoring (justified)
- src/legacy/* — protected area (see CLAUDE.md)
```

## Rules
1. **Don't refactor without tests.** Green tests are your safety net.
2. **One smell per commit** — easier to review and revert.
3. **Rule of 3 for duplication** — 2 is fine; 3+ warrants extraction.
4. **Prefer deletion to refactoring** — simplest code is no code.
5. **Stop when it's good enough** — perfect is a smell too.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "It works, don't touch it" | Working != maintainable; debt compounds |
| "I'll refactor later" | Later = next dev curses you |
| "It's only 60 lines, not THAT long" | 60 is 20% past the limit; fix now |
| "The names are fine" | Your future self won't remember context |
| "Adding an abstraction helps" | Extracting one usage is premature; wait for 3 |

## Integration
- Runs AFTER `verification-before-completion` (tests green first)
- Runs BEFORE `code-review` (review the simplified version)
- Feeds into PR description: "Simplified: <list smells addressed>"
