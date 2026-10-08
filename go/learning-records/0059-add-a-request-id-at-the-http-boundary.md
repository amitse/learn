# Learning Record 0059 — Add a Request ID at the HTTP Boundary

## Date
2026-10-08

## Lesson
0059 — Add a Request ID at the HTTP Boundary

## What was taught

- A server-generated request ID is correlation metadata: it lets an operator join a client report to a structured server event without returning the private error cause.
- One `RequestID` middleware wraps the mux, generates an opaque value, sets the `X-Request-ID` response header before delegation, and passes a derived request with `r.WithContext(ctx)` to downstream handlers.
- Store the value with `context.WithValue` using an unexported custom key type such as `requestIDKey{}`; expose a narrow `RequestIDFromContext` accessor rather than sharing string keys.
- `crypto/rand.Read` plus `hex.EncodeToString` produces a server-issued header-safe value. The initial design deliberately does not trust caller-supplied IDs.
- An unexpected-error event adds `"request_id", RequestIDFromContext(r.Context())` alongside its private `error` attribute, while the existing public JSON 500 remains generic.
- The focused contract test asserts a non-empty `X-Request-ID` response header and the same value in the decoded structured log event, while retaining the no-private-cause assertion from Lessons 56–58.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `req.requestId = crypto.randomUUID()` | derived request context with `context.WithValue` |
| `res.setHeader("X-Request-ID", id)` | `w.Header().Set("X-Request-ID", id)` before `next.ServeHTTP` |
| request-local property | private key type plus `RequestIDFromContext(ctx)` accessor |
| log `{ requestId, err }` | `logger.Error(..., "request_id", id, "error", err)` |

## Key insight

A request ID is not an error explanation and should not become a client-controlled logging label. It is one opaque, boundary-created correlation value: safe to return and log, useful for support, and separate from the private error cause.

## Zone of proximal development notes

Amit already has decoded structured error-event tests and a public/private error boundary. Adding one boundary-owned request value is the smallest practical extension: it links the safe public response to the private diagnostic event without changing the JSON error contract or introducing distributed tracing.

## What comes next

- Add a testable ID-generator dependency if exact deterministic IDs are preferred in handler tests.
- Decide with deployment operators whether a trusted proxy should propagate an upstream correlation ID, including validation and trust-boundary rules.
- Apply the same `request_id` attribute to other request-scoped error events, keeping lifecycle events distinct.
