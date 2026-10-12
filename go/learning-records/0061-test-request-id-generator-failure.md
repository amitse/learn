# Learning Record 0061 — Test Request-ID Generator Failure

## Date
2026-10-12

## Lesson
0061 — Test Request-ID Generator Failure

## What was taught

- A request-ID generator failure happens before the middleware can safely set `X-Request-ID`, derive the request context, or delegate to application code.
- Inject a generator that returns a distinctive `errors.New(...)` value to make this rare failure branch deterministic; no real entropy failure is needed in a test.
- The public contract is the existing generic JSON 500 envelope, never `err.Error()`; the distinctive private cause must be absent from the response body.
- A boolean or small spy downstream handler lets the test require that `next.ServeHTTP` was not called after the prerequisite failed.
- If operational diagnostics are needed, record the cause only as a private structured log attribute; this does not change the client response contract.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `try { makeID(); next() } catch { res.status(500).json(safe) }` | `id, err := makeID(); if err != nil { writeJSONError(...); return }` |
| stub that throws a selected `Error` | closure returning `"", errors.New(privateCause)` |
| mock assertion that `next()` was not called | boolean / spy handler assertion that `next.ServeHTTP` was not called |

## Key insight

A failed boundary prerequisite is a stop condition, not a reason to continue with partial request state. Test both sides of that promise: a safe generic response for the client and no downstream execution without an issued correlation ID.

## Zone of proximal development notes

Amit already has a generator seam, a generic JSON error envelope, decoded error-contract tests, and private-error containment tests. Combining those known pieces into one early-return test is the smallest next step in making request-ID middleware trustworthy on its failure path.

## What comes next

- Emit and decode a private structured log event for a request-ID generator failure, while preserving the generic 500 response.
- Decide whether the request-ID generator should have a failure metric; keep its label set low-cardinality and never use the error text as a metric label.
- Only after an explicit trust policy, consider accepting and validating a trusted proxy's upstream correlation ID.
