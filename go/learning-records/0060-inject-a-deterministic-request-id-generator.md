# Learning Record 0060 — Inject a Deterministic Request-ID Generator

## Date
2026-10-10

## Lesson
0060 — Inject a Deterministic Request-ID Generator

## What was taught

- Request-ID middleware needs one small behavior dependency: `type IDGenerator func() (string, error)`.
- Production wiring passes a secure `NewRandomID` implementation using `crypto/rand`; a focused test passes a closure returning a known value such as `req-test-42`.
- The middleware creates one ID, sets `X-Request-ID`, derives the request context with that same value, and delegates; the downstream handler must not generate another ID.
- Exact assertions against the fixed value verify propagation through the response header and request context more strongly than a non-empty assertion.
- A generator failure can return the existing safe generic 500 response; its private cause must not be exposed to the client.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `type IDGenerator = () => string` | `type IDGenerator func() (string, error)` |
| inject `() => "req-test-42"` | inject `func() (string, error) { return "req-test-42", nil }` |
| test a fixed `res.getHeader(...)` value | assert exact `httptest.ResponseRecorder` header and request-context value |

## Key insight

Randomness is a production implementation detail, not a test input. Injecting the smallest behavior that creates an ID makes the correlation contract exact and deterministic while keeping production IDs securely random.

## Zone of proximal development notes

Amit already created a boundary-owned request ID and decoded the corresponding structured error event. The smallest next step is to replace a probabilistic “non-empty” test with a precise propagation test, using a narrow function dependency instead of a global or a mock framework.

## What comes next

- Add a focused test for an ID-generator failure that retains the safe generic error response and does not call the downstream handler.
- Extend the decoded unexpected-error event assertion to require the known generated ID.
- Decide separately whether a trusted proxy may supply an upstream ID; that requires explicit validation and trust-boundary policy.
