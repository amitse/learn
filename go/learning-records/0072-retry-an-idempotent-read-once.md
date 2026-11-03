# Learning Record 0072 — Retry an Idempotent Read Once

## Date
2026-11-03

## Lesson
0072 — Retry an Idempotent Read Once

## What was taught

- A retry must be attached only to an operation whose owner has explicitly decided that repeating it is safe; a read-only profile lookup is the narrow example, not a blanket rule for HTTP requests.
- A bounded wrapper makes at most two total calls: attempt one, then one additional call only when the error is classified as retryable.
- The wrapper reuses the caller's `context.Context`; a direct or wrapped `context.Canceled` / `context.DeadlineExceeded` error stops before attempt two through the classifier from lesson 71.
- Injecting the dependency operation as a named function type creates a small deterministic test seam: assert the result and the exact call count without a server, delay, or `time.Sleep`.
- This is control flow, not a complete retry policy: it intentionally excludes backoff, HTTP-status policy, and generic retries of side-effecting writes.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `try` a read, then conditionally call it once more | call `get`, return early on success/non-retryable error, then call `get` once more |
| injected async function / mocked fetch wrapper | injected `func(context.Context, string) (Profile, error)` |
| `AbortSignal` retained across attempts | same `context.Context` passed to both attempts |

## Key insight

“Two attempts” is a maximum, not a promise. The first failure earns a second call only after an operation-specific safety decision and the existing stop-signal classifier both permit it.

## Zone of proximal development notes

Amit already distinguishes wrapped transport errors from cancellation/deadline exhaustion (lessons 68–71). This lesson adds only the smallest observable retry behavior—one conditional retry—and tests it through a call counter before introducing waiting or more attempts.

## What comes next

- Inject a backoff/wait function so a bounded retry policy can record a delay without sleeping in tests.
- Keep an exhausted retry outcome distinct from cancellation and deadline mapping at the HTTP boundary.
