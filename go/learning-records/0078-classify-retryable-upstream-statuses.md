# Learning Record 0078 — Classify Retryable Upstream Statuses

## Date
2026-11-15

## Lesson
0078 — Classify Retryable Upstream Statuses

## What was taught

- An HTTP dependency may return a non-2xx response without `http.Client.Do` returning an error; response-status policy must therefore be explicit in the dependency adapter.
- `retryableUpstreamStatus` is a pure classifier that approves only `429`, `502`, `503`, and `504` as candidates for the existing single retry of the idempotent profile read.
- A table test names both approved and deliberately excluded statuses, especially `501` and `500`, preventing an accidental broad “all 5xx” policy.
- A response-status classifier only decides retry candidacy. The adapter owns response-body cleanup and translation to a dependency error; the retry wrapper still owns the two-attempt limit.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `includes(response.status)` before throwing a typed retryable error | `switch resp.StatusCode` in `retryableUpstreamStatus` |
| `fetch` resolves with a 4xx/5xx response | `http.Client.Do` can return a non-nil response with `err == nil` |
| table-driven parameterized test | `[]struct` plus `t.Run` |

## Key insight

HTTP status alone does not grant retry permission. Enumerating a small set of temporary upstream statuses makes the operational behavior reviewable, bounded, and resistant to accidental policy expansion.

## Zone of proximal development notes

Amit has established conservative error retryability (71), one bounded idempotent-read retry (72), a cancellable delay (73–75), and the exhausted-retry boundary and observability contracts (76–77). This lesson adds the missing response-path classification without changing attempt count or public error mapping.

## What comes next

- Translate an approved upstream response status into a typed dependency error while closing its response body.
- Test that an approved status reaches the existing one-retry path, whereas a nearby excluded status does not.
