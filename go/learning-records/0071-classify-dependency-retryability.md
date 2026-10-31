# Learning Record 0071 — Classify Dependency Retryability

## Date
2026-10-31

## Lesson
0071 — Classify Dependency Retryability

## What was taught

- Retryability is a policy decision made before implementing a retry loop; a classifier should not sleep, loop, or make another request.
- `errors.Is` recognizes `context.Canceled` and `context.DeadlineExceeded` through the `%w` error chain created at the dependency boundary.
- This lesson's conservative policy makes both cancellation and deadline exhaustion non-retryable, because the caller or the allocated time budget has ended the work.
- A selected transport failure is only a candidate for a future retry policy; bounded attempts, backoff, idempotency, and HTTP-status policy remain separate decisions.
- A compact table test protects the distinction among no error, wrapped stop signals, and a wrapped transport failure without matching error text.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| inspect abort/timeout error identity | `errors.Is(err, context.Canceled)` / `errors.Is(err, context.DeadlineExceeded)` |
| wrapped error with a cause | `fmt.Errorf("get profile: %w", err)` |
| retry eligibility predicate | pure `func retryableDependencyError(error) bool` |

## Key insight

A timeout is not evidence that another immediate attempt will help: it is evidence that the allocated deadline has expired. Preserve that meaning by classifying cancellation and deadlines before any retry machinery exists.

## Zone of proximal development notes

Amit has already created and tested wrapped dependency errors (68), cancellation propagation (69), and a child dependency deadline (70). This lesson reuses only those error identities to add one narrow policy decision, without introducing retry loops, sleeps, or idempotency mechanics.

## What comes next

- Implement a bounded retry loop for an explicitly safe, selected dependency operation.
- Inject a backoff function so retry timing can be tested without sleeping.
- Keep an exhausted retry policy distinct from cancellation and deadline mapping at the HTTP boundary.
