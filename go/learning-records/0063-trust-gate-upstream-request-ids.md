# Learning Record 0063 — Trust-Gate Upstream Request IDs

## Date
2026-10-16

## Lesson
0063 — Trust-Gate Upstream Request IDs

## What was taught

- A non-empty `X-Request-ID` is request input, not evidence that a trusted proxy supplied it.
- Reuse an upstream ID only after an outer infrastructure boundary has attached a server-owned `trustedProxy` fact; otherwise choose the newly generated server ID.
- Isolate the selection policy in a pure `chooseRequestID` function and table-test the trusted, untrusted, and missing-upstream-ID branches.
- A private typed context key can transport an already-established trust decision through Go middleware. The marker is not authentication and must only be set by an edge that enforced the proxy boundary.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| trusted ingress middleware sets `req.trustedProxy` | edge middleware derives `r.WithContext(context.WithValue(...))` using a private key |
| pure selector receives `{ trustedProxy, upstreamID, generatedID }` | pure selector receives `trustedProxy bool, upstreamID, generatedID string` |
| a client header is untrusted request input | `r.Header.Get("X-Request-ID")` returns a value, not its provenance |

## Key insight

Correlation continuity is valuable only when its origin is explicit. A request header cannot grant itself trust: preserve it only when server-controlled infrastructure has established that it came through the expected boundary.

## Zone of proximal development notes

Amit already has deterministic ID generation, failure behavior, safe public errors, context metadata, and table-driven tests. This lesson adds one narrow policy seam before any upstream ID is allowed into those existing response and logging paths, without introducing proxy networking configuration or ID-format validation.

## What comes next

- Define a bounded allowed format for an upstream ID that has passed the trust gate.
- Validate and normalize that value before it becomes a response header, context value, or log attribute.
- Consider an explicit low-cardinality metric for generator failures; never make an error string a metric label.
