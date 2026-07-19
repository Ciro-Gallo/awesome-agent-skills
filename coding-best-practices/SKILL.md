---
name: coding-best-practices
description: Use this skill whenever an AI agent is expected to write, modify, refactor, extend code or commit code. This includes bug fixes, new features, multi-file edits, tests, refactors, migrations, and configuration changes. Trigger even when the user does not explicitly mention "best practices" if the task requires producing production-grade code, preserving existing behavior, following local conventions, or validating the result before handoff. Avoids triggering for things like analysis-only, docs-only, or code explanation requests.
metadata:
  author: Ciro Gallo
  version: "1.0.0"
---

# Coding Best Practices

Write code like a careful teammate who has to live with the result later. Favor correctness, clarity, and safe integration over cleverness or speed.

## Hard security rule

It is absolutely forbidden to commit credentials or secrets into code, tests, configuration, fixtures, examples, or documentation. This includes tokens, API keys, passwords, private keys, session secrets, and similar sensitive values.

If code needs a credential, use the project's existing secret-management pattern such as environment variables, secret stores, or injected runtime configuration. If the user asks for a commit and there is a risk of committing credentials, warn the user.

## Core goals

1. Solve the user's actual problem, not just a nearby symptom.
2. Fit the existing codebase instead of imposing a new style or architecture without a reason.
3. Keep changes small-but-complete: cover the implementation, any directly affected tests, and adjacent docs or configuration when behavior changes.
4. Validate with the project's existing checks before concluding.

## Recommended workflow

### 1. Understand the request before editing

- Read the task carefully and identify the expected behavior, constraints, and likely touch points.
- Inspect the relevant code, tests, and nearby patterns before proposing or making changes.
- If behavior is ambiguous and the choice materially affects the result, ask the user instead of guessing.

### 2. Match local patterns

- Reuse existing helpers, utilities, abstractions, and naming conventions when they already solve part of the problem.
- Keep the solution consistent with the surrounding architecture, error handling, and formatting.
- Do not add a new dependency, framework, or helper when an established local pattern already exists.

### 3. Plan proportionally

- For a tiny single-file fix, a mental plan is enough.
- For multi-file or riskier work, outline the work first: what changes, what must stay stable, and how you will validate the result.
- When a task breaks naturally into steps, track them so nothing important is forgotten.

### 4. Make surgical code changes

- Change the smallest coherent surface that fully addresses the request.
- Prefer root-cause fixes over cosmetic patches.
- Preserve public behavior unless the task explicitly asks for a behavior change.
- If behavior does change, update the relevant tests and documentation in the same pass.

### 5. Keep the code safe and maintainable

- Handle invalid input explicitly and in a way that matches the local codebase.
- Avoid broad exception handling, silent fallbacks, and "success-shaped" failures.
- Preserve type safety and function contracts; do not introduce unnecessary casts or weakly typed shortcuts.
- Avoid duplication when a shared helper or extraction would make the code clearer.
- Do not leave behind debug code, commented-out experiments, or half-finished TODO paths.

### 6. Test the real behavior

- Prefer existing tests over inventing a brand-new test stack.
- Add or update tests when the task changes behavior, closes a bug, or introduces logic that should not regress.
- Run the narrowest existing checks that give high confidence first, then broader project checks when warranted.
- If validation fails, fix the underlying issue before handing off.

### 7. Finish with a clear handoff

- Summarize the meaningful change, not every tiny edit.
- Call out any intentional tradeoff, limitation, or follow-up only when it matters.
- Do not present speculative work as complete.

## Decision heuristics

Use these tie-breakers when several implementations look plausible:

- Prefer the option that is easiest for the next engineer to understand.
- Prefer stable interfaces over breaking ones.
- Prefer explicit validation over permissive guessing.
- Prefer existing project tooling over ad hoc scripts or new dependencies.
- Prefer a slightly more complete fix over a narrowly passing patch when the adjacent work is directly coupled.

## Concrete coding use cases

Use these patterns to translate the principles above into real implementation choices.

### 1. Adding validation without breaking callers

When a function accepts user-controlled data, keep the public shape stable but reject invalid input explicitly.

**Weak:**

```js
function createUser(input) {
  return db.insert(input);
}
```

**Better:**

```js
function createUser(input) {
  if (!input.email) {
    throw new Error("email is required");
  }

  return db.insert({ ...input, email: input.email.trim().toLowerCase() });
}
```

Why: this preserves the contract while making failure predictable and safer.

### 2. Reusing existing helpers instead of duplicating logic

If the codebase already has a parser, validator, formatter, or mapper, route new behavior through it instead of adding a second version.

**Weak:**

```js
const timeoutMs = Number.parseInt(process.env.TIMEOUT_MS, 10) || 3000;
```

**Better:**

```js
const timeoutMs = parsePositiveInt(process.env.TIMEOUT_MS, input.timeoutMs ?? 3000);
```

Why: shared logic keeps edge cases and defaults consistent across the codebase.

### 3. Preserving backward compatibility while extending behavior

When adding a feature to an existing API, prefer additive changes over signature-breaking rewrites.

**Weak:**

```py
def finalize_total(subtotal, loyalty, coupon_percentage):
    ...
```

**Better:**

```py
def finalize_total(subtotal, loyalty):
    return finalize_total_with_coupon(subtotal, loyalty, 0)

def finalize_total_with_coupon(subtotal, loyalty, coupon_percentage):
    ...
```

Why: existing callers keep working, while new behavior gets a clearer entry point.

### 4. Fixing performance problems at the query boundary

When code loops over records and performs one expensive operation per item, look for batching, pagination, or filtering near the data source.

**Weak:**

```js
for (const user of users) {
  user.posts = await getPosts(user.id);
}
```

**Better:**

```js
const postsByUserId = await getPostsForUsers(users.map((user) => user.id));
```

Why: performance fixes are usually safer and more effective at the boundary than inside the loop body.

### 5. Refactoring large functions by separating responsibilities

If a function validates input, performs business logic, writes to storage, and sends notifications, split it into focused helpers before adding more behavior.

**Weak:**

```js
function processUserDataAndSendEmailAndSave() {}
```

**Better:**

```js
function processUserData() {}
function saveUser() {}
function sendWelcomeEmail() {}
```

Why: smaller units are easier to test, reuse, and reason about when fixing bugs.

### 6. Writing tests for behavior, not internals

When you change logic, prefer tests that describe externally visible behavior and failure modes.

**Weak:**

```js
test("calls private helper", () => {});
```

**Better:**

```js
test("returns 400 when coupon percentage is out of range", () => {});
test("uses environment timeout when the override is valid", () => {});
```

Why: behavior-oriented tests survive refactors and catch regressions users actually feel.

### 7. Handling errors explicitly and usefully

Do not swallow exceptions or return ambiguous failure values if the codebase expects explicit errors.

**Weak:**

```js
try {
  saveOrder(order);
} catch (error) {}
```

**Better:**

```ts
try {
  saveOrder(order);
} catch (error) {
  const message = error instanceof Error ? error.message : String(error);
  logger.error({ orderId: order.id, err: message });
  throw new Error(`Failed to save order ${order.id}`, { cause: error });
}
```

Why: explicit failures are easier to debug and less likely to hide corrupted state.

### 8. Choosing simpler code over clever code

Prefer readable branches, descriptive names, and straightforward data flow over compressed expressions.

**Weak:**

```js
const result = a ? b : c ? d : e;
```

**Better:**

```js
if (a) return b;
if (c) return d;
return e;
```

Why: future edits are safer when the next engineer can see intent immediately.

## Anti-patterns to avoid

- Editing code before reading the surrounding context
- Rewriting unrelated areas "while you're here"
- Ignoring existing helpers and duplicating logic
- Changing behavior without updating tests
- Claiming completion without running the project's existing checks
- Swallowing errors or hiding edge cases behind defaults
- Overengineering a simple request with new abstractions that the codebase does not need

## Output expectations

When you finish a coding task with this skill active:

1. Deliver the code change itself.
2. Mention the main behavioral or structural improvement.
3. Mention a next step only if something remains blocked, risky, or intentionally deferred.

## Example situations where this skill should help

- "Add support for a new API field without breaking old callers."
- "Fix the failing parser test and make the logic less brittle."
- "Refactor this service method, but keep the public response shape stable."
- "Update this config loading path and make sure invalid input is surfaced clearly."
- "Replace an N+1 query with a batched lookup without changing the response shape."
- "Add validation to an endpoint and return useful errors instead of silent fallbacks."
- "Extend a function with new behavior while preserving compatibility for existing callers."
- "Split a large function into smaller helpers and update tests to cover the real behavior."

## Extended reference

For a larger catalog of concrete examples across security, performance, architecture, testing, error handling, API contracts, and maintainability, read `useful-sample.md`.
