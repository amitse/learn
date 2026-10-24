# Learning Record 0067 — Propagate the Selected Request ID Downstream

## Date
2026-10-24

## Lesson
0067 — Propagate the Selected Request ID Downstream

## What was taught

- The inbound request-ID middleware makes the correlation decision once; an outbound HTTP call must propagate that selected context value, not reread and forward the raw inbound header.
- `http.NewRequestWithContext` creates a fresh outbound request carrying the caller's cancellation and deadline context. Setting `X-Request-ID` from `RequestID(ctx)` makes the propagation explicit.
- Do not copy an inbound request's entire header map into an outbound request. It can forward unrelated credentials or connection-specific headers, and it bypasses the selected-ID policy.
- An `httptest.NewServer` test can observe the actual request received by a downstream handler and assert the wire-level `X-Request-ID` value without a real network dependency.
- W3C trace context is a separate interoperability contract with its own grammar and trust rules; it should not be conflated with this application request-ID header.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `fetch(url, { headers: { "X-Request-ID": req.context.requestId } })` | `http.NewRequestWithContext(ctx, method, url, nil)` then `req.Header.Set("X-Request-ID", RequestID(ctx))` |
| captured downstream test route | `httptest.NewServer(http.HandlerFunc(...))` |
| selected request property | private context accessor returning the middleware-selected ID |

## Key insight

Propagation is not forwarding. The outbound boundary carries the single server-selected correlation value so the client response, private events, and downstream call identify the same request—even when raw inbound input was rejected.

## Zone of proximal development notes

Amit has already implemented and table-tested trusted/valid request-ID selection and its two middleware-owned outputs. This lesson extends that established invariant by one concrete boundary—an outbound HTTP request—without adding distributed tracing, retries, or an HTTP client abstraction.

## What comes next

- Add an outbound error wrapper that preserves the transport cause while naming the dependency operation.
- Test that cancellation of the inbound request context reaches a deliberately blocked downstream request.
- If a dependency requires W3C Trace Context, create and test a distinct trace-context adapter with its own explicit trust policy.
