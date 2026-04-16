---
name: code-review
description: Multi-pass code review with severity tags. Use before committing to catch logic bugs, edge cases, and design issues that tests don't cover.
license: MIT
model: opus
effort: high
---

# Code Review (Multi-Pass)

## Purpose
Catch bugs that automated tests miss: edge cases, race conditions, null handling, security holes, and design flaws. Three focused passes instead of one vague scan.

## When to Use
- Before every `git commit` on non-trivial changes
- Before opening a PR
- After implementing a feature/fix
- When user says "review this code" or "antes de commitear"

## The 3 Passes

### Pass 1 — Correctness (blocking bugs)
Focus: Does this code do what it claims, always?

Check for:
- **Null/undefined access** — `obj.prop` without guard
- **Off-by-one errors** — loops, array bounds, pagination
- **Async bugs** — missing `await`, unhandled promise rejections
- **Race conditions** — shared state, concurrent writes, stale closures
- **Edge cases** — empty arrays, zero, negative, Unicode, very large inputs
- **Error paths** — what happens when the 3rd-party call fails?
- **Resource leaks** — unclosed connections, listeners, timers
- **Input validation** — is user input sanitized at the boundary?

### Pass 2 — Design (important issues)
Focus: Is this code maintainable?

Check for:
- **Single responsibility** — function doing >1 thing
- **Leaky abstractions** — internal details exposed
- **Tight coupling** — impossible to test in isolation
- **Missing types** — `any`, `unknown` that should be concrete
- **Magic numbers/strings** — should be named constants
- **Duplicated logic** — copy-paste that drifts
- **Premature abstraction** — generic code used once

### Pass 3 — Style (nits)
Focus: Is this code readable?

Check for:
- **Naming** — unclear, abbreviated, misleading
- **Comments** — stale, obvious, or missing WHY
- **Dead code** — unused imports, unreachable branches
- **Formatting** — inconsistent with codebase

## Severity Tags (use verbatim)

| Tag | Meaning | Action |
|-----|---------|--------|
| `[BLOCKING]` | Bug or security hole. Must fix before commit. | Fix now |
| `[IMPORTANT]` | Design issue that will cause pain later. | Fix or justify |
| `[NIT]` | Style/naming preference. | Optional |
| `[SUGGESTION]` | Alternative approach worth considering. | Discuss |
| `[LEARNING]` | Context/rationale for future reference. | Read |
| `[PRAISE]` | Good decision worth highlighting. | Encourage |

## Output Format

```
## Code Review: <file path or feature>

### Pass 1 — Correctness
[BLOCKING] src/auth.ts:42 — `user.email` accessed without null check; throws on guest users.
[IMPORTANT] src/api/route.ts:15 — missing `try/catch` around external fetch; single timeout crashes request.

### Pass 2 — Design
[IMPORTANT] src/payment.ts:88 — function does validation + DB write + email send. Split into 3.
[NIT] src/utils/format.ts:12 — `fn1` is too generic; rename to `formatCurrency`.

### Pass 3 — Style
[NIT] src/components/Card.tsx:5 — unused import `useState`.
[PRAISE] src/db/query.ts:20 — excellent use of prepared statements.

### Summary
- 1 BLOCKING (must fix)
- 2 IMPORTANT (fix or justify)
- 2 NITs (optional)
- Ready to commit after BLOCKING is addressed.
```

## Rules
1. **Be specific** — file:line, not "somewhere in auth".
2. **One finding per line** — easier to track.
3. **Never invent bugs** — if unsure, mark `[SUGGESTION]`, not `[BLOCKING]`.
4. **Praise good patterns** — reinforces what to repeat.
5. **Review diff, not full file** — stay focused on changes.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "It's just a small fix, doesn't need review" | Small fixes cause ~40% of production bugs |
| "Tests pass, so it's fine" | Tests only cover what you thought to test |
| "I'll add the null check later" | Later never comes; it ships with the bug |
| "This is obviously safe" | If it's obvious, explaining it costs 5s |
| "The reviewer will catch issues" | You ARE the reviewer here; no safety net |

## Integration with Workflow
- Run AFTER implementing, BEFORE `git commit`
- If `[BLOCKING]` found → fix and re-run Pass 1 on the fix
- If only `[NIT]` → commit with note "Addressed blocking/important; nits deferred"
- For PRs: paste output in PR description under "Self-review"
