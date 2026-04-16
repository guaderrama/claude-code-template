---
name: pre-mortem
description: Simulate failure before implementing. Lists the 5 most likely failure modes of a feature/plan before writing code, forcing adversarial thinking that prevents bugs by assumption.
license: MIT
model: sonnet
effort: medium
---

# Pre-Mortem Analysis

## Purpose
Before implementing a non-trivial feature, pretend it already failed in production and work backwards: **why did it fail?** This surfaces assumptions, edge cases, and integration risks that normal planning misses.

## When to Use
- Before implementing any feature touching: auth, payments, data migration, concurrency, external APIs, user data
- Before large refactors
- When the user says "pre-mortem", "analiza riesgos", "qué puede fallar", "antes de empezar esto"
- When the plan you're about to execute touches any category in "Failure Mode Categories" below

## The Method

### Step 1 — Write the Failure Scenario
One sentence, past tense, specific:
> "It's 3 weeks after launch. The feature failed catastrophically. Users are angry. What happened?"

### Step 2 — List 5 Failure Modes
For each mode, answer:
- **What broke?** (technical description)
- **Why?** (root cause / missed assumption)
- **How was it detected?** (user complaint / monitoring / audit)
- **Blast radius** (1 user / all users / data loss / money loss)

### Step 3 — Classify by Likelihood × Impact
```
             Low Impact   High Impact
Likely:      [ignore]     [MUST MITIGATE]
Unlikely:    [ignore]     [mitigate if cheap]
```

### Step 4 — For Each "MUST MITIGATE", Plan a Defense
- Test case that would catch it
- Code-level guard (validation, transaction, retry)
- Monitoring/alert to detect in prod
- Rollback plan

## Failure Mode Categories (checklist)

Use these as prompts when brainstorming:

### Data
- [ ] Input at 0 / null / empty / very large / Unicode / SQL chars?
- [ ] What if two users act simultaneously on same resource?
- [ ] Schema migration runs on 10M rows — how long does it lock?
- [ ] Backup/rollback if migration corrupts data?

### External
- [ ] 3rd-party API down / slow / rate-limited / returns garbage?
- [ ] API changes response format without notice?
- [ ] Retries cause duplicate charges / sends / writes?

### Auth/Security
- [ ] Logged-out user hits this endpoint?
- [ ] User A tries to access User B's data?
- [ ] Token expires mid-request?
- [ ] Admin-only flow accessible via URL manipulation?

### Performance
- [ ] What if the query returns 1M rows?
- [ ] What if a user uploads a 10GB file?
- [ ] Does this cache invalidate correctly?
- [ ] N+1 queries hidden in a loop?

### State
- [ ] What if the request fails halfway?
- [ ] What if the user refreshes during submission?
- [ ] Double-click on submit?
- [ ] Browser back button after success?

### Deployment
- [ ] Old client + new server during deploy window?
- [ ] New client + old server (rollback)?
- [ ] Feature flag off for some users, on for others?

## Output Format

```markdown
## Pre-Mortem: <feature name>

**Scenario:** <1 sentence, past tense failure>

### Failure Modes
1. **<name>** — What: ... Why: ... Detection: ... Blast: <size>
2. ...

### Must Mitigate (High likelihood × High impact)
- [ ] Mode #1 — Defense: <test + code guard + monitoring>
- [ ] Mode #3 — Defense: ...

### Acceptable Risks (documented, not fixed)
- Mode #5 — Rare + low impact; accept.

### Ready to Implement?
- [ ] All "Must Mitigate" items have a plan
- [ ] Test cases written for each
```

## Rules
1. **Be specific, not generic.** "It could fail" is not a mode. "POST /api/payment returns 500 when Stripe webhook arrives before order is inserted" IS.
2. **Think like an attacker and a clumsy user.** Both find bugs engineers miss.
3. **Question EVERY assumption.** "The ID will be unique" — will it? Under concurrency?
4. **Time-box to 10 minutes.** Pre-mortem is a trigger, not a thesis.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Too early for this, let me just build it" | 10 min now saves 10 hours of debugging |
| "The happy path works, edge cases are rare" | Rare × millions of users = daily incidents |
| "We'll add monitoring later" | Later = after the first outage |
| "This is too small to fail badly" | Payment bugs are "small" until they aren't |
| "I already thought about risks" | Writing them down exposes gaps thinking alone hides |

## Integration
- Output of pre-mortem → inputs to TDD (each failure mode = a test case)
- Output of pre-mortem → inputs to code-review Pass 1 (explicitly check each mode)
- Keep the pre-mortem doc in the PR description for reviewers
