# Learning Record 0075 — Choose a Fixed Retry Delay

## Date
2026-11-09

## Lesson
0075 — Choose a Fixed Retry Delay

## What was taught

- The existing two-attempt idempotent-read policy has exactly one possible gap between attempts, so a named fixed delay is the smallest explicit timing policy.
- Choose the value at the application composition boundary, for example `const profileRetryDelay = 100 * time.Millisecond` and `timerWait(profileRetryDelay)`.
- The duration is a delay after a first approved transient failure, not a dependency timeout, a retry count, or authorization to retry a write.
- The retry wrapper continues to own control flow: first call → retryable failure → cancellable wait → one second call.
- Test the wrapper using its injected immediate `retryWait`, asserting `get, wait, get` and context identity; do not make a test wait for the production duration.
- A one-retry policy deliberately excludes exponential backoff and jitter; cancellation and deadline exhaustion remain non-retryable.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `const delayMs = 100` wired into an abort-aware wait | `const delay = 100 * time.Millisecond` wired into `timerWait` |
| composition creates a client with a wait behavior | composition passes `timerWait(profileRetryDelay)` to the Go client/wrapper |
| immediate fake promise records retry order | immediate `func(context.Context) error` fake records `get, wait, get` |

## Key insight

A retry delay is operational policy, not transport mechanics. Selecting it once at composition makes the cost visible and leaves the retry algorithm deterministic to test; the request context remains the final authority to stop waiting.

## Zone of proximal development notes

Amit already has an explicit two-attempt idempotent read (72), an injectable cancellable wait (73), and its timer implementation (74). This lesson connects those seams with one smallest practical policy choice without expanding the retry count or introducing backoff mathematics.

## What comes next

- Decide how an exhausted approved retry maps to the existing safe JSON HTTP error contract while retaining its private cause.
- If retry policy later gains multiple attempts, define a bounded deterministic base-delay policy before considering jitter.
