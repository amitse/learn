# Learning Record 0074 — Implement a Cancellable Timer Wait

## Date
2026-11-07

## Lesson
0074 — Implement a Cancellable Timer Wait

## What was taught

- A production retry delay can implement the existing `retryWait` seam with `time.NewTimer` and a `select` over `timer.C` and `ctx.Done()`.
- If the timer fires, the wait returns `nil`; if the request context is canceled or its deadline expires first, it returns `ctx.Err()` immediately.
- `defer timer.Stop()` stops a still-pending timer on the cancellation path; the wait function returns and does not drain the timer channel after that return.
- An already-canceled context makes a deterministic test: configure a deliberately long duration, invoke the wait, and assert `errors.Is(err, context.Canceled)` without `time.Sleep`.
- The timer implements one chosen delay only. Retry eligibility, the two-attempt bound, duration selection, exponential backoff, and jitter remain separate policies.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `setTimeout` raced with `AbortSignal` | `time.NewTimer` selected against `ctx.Done()` |
| clear pending timeout on abort | `defer timer.Stop()` on return |
| abort rejection stops retry | return `ctx.Err()` before the second `get` |

## Key insight

A retry delay belongs to the request’s cancellation tree. If a client has stopped waiting, a server should stop waiting too—not retain work solely to finish a backoff timer.

## Zone of proximal development notes

Amit already has an injected, cancellable retry-wait seam (73), so this lesson supplies its smallest real implementation without changing retry count or introducing backoff mathematics. The test reuses the established no-sleep testing principle and the existing `context.Canceled` identity.

## What comes next

- Choose and test one bounded retry-delay policy while keeping it separate from timer mechanics.
- Decide how an exhausted approved retry should map at the HTTP boundary.
- Add jitter only after a deterministic base-delay policy and its tests are clear.
