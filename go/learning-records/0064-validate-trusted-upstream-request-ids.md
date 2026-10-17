# Learning Record 0064 — Validate Trusted Upstream Request IDs

## Date
2026-10-17

## Lesson
0064 — Validate Trusted Upstream Request IDs

## What was taught

- The trusted-proxy gate from Lesson 63 answers who supplied an upstream request ID; a separate validator answers whether that value is acceptable for this application's response header, request context, and logs.
- A bounded application policy accepts only 1–128 ASCII characters from `A–Z`, `a–z`, `0–9`, `-`, `_`, and `.`. It intentionally rejects whitespace, control characters, arbitrary Unicode, and oversized values.
- The request-ID middleware first obtains its secure generated fallback, then reuses an upstream value only when both the server-owned trusted-proxy marker and `validUpstreamID` are true.
- An invalid value is not trimmed or repaired: reuse of a modified identifier would make logs and the client response describe a value not actually supplied by the proxy. The safe fallback is the generated server ID.
- A table test captures the combined policy: trusted + allowed preserves the upstream ID; trusted + malformed/oversized and untrusted + allowed-looking values both use the generated ID.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `/^[A-Za-z0-9._-]{1,128}$/.test(id)` | `validUpstreamID` length check plus rune allowlist loop |
| `trusted && validID(upstream) ? upstream : generated` | trusted-marker and validator condition selecting the generated fallback |
| reject input rather than `trim()` it | preserve exact accepted bytes; invalid input never becomes a silently altered correlation key |

## Key insight

A trusted source does not make every value safe. Reuse an upstream correlation ID only when its provenance is server-established and its exact value meets a small, documented contract; otherwise preserve continuity inside the service with a newly generated ID.

## Zone of proximal development notes

Amit already has deterministic generation, a private failure path, table-driven policy tests, and an explicit trust gate. A narrow validator is the next boundary layer: it completes safe selection before accepted IDs propagate to the client response, request context, and private events, without introducing a proxy-specific tracing standard.

## What comes next

- Add an observable, low-cardinality reason for upstream-ID rejection only if the metrics boundary is already available; never label a metric with the rejected value.
- Test end-to-end that only a selected ID, not an invalid upstream value, reaches both `X-Request-ID` and a decoded error log event.
- If deployment requires a standard trace context, define a distinct adapter rather than treating its identifier grammar as interchangeable with this application policy.
