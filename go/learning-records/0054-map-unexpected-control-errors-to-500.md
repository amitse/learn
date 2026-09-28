# Learning Record 0054 — Map Unexpected Control Errors to 500

## Date
2026-09-28

## Lesson
0054 — Map Unexpected Control Errors to 500

## What was taught

- The operations handler maps only its known authorization sentinel, `ErrForbidden`, to HTTP 403; `errors.Is` preserves this mapping when the sentinel is wrapped.
- Every other non-nil `LogLevelControl.Set` error is an unexpected internal failure and maps to HTTP 500.
- The route must not expose `err.Error()` in the 500 response. Detailed error evidence belongs in a structured server-side log.
- A fourth `httptest` table row supplies an authenticated operator and an internal sentinel, then asserts 500, one control call, and the route’s selected empty-body contract.
- `http.Error` writes a text response; use `WriteHeader(http.StatusInternalServerError)` when the deliberately tested contract is an empty body.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `if (err === forbidden)` | `if errors.Is(err, ErrForbidden)` |
| error middleware / `res.sendStatus(500)` | handler fallback plus `w.WriteHeader(http.StatusInternalServerError)` |
| structured logger with `{ err }` | `logger.Error("…", "error", err)` |

## Key insight

An HTTP status is an intentional public contract, not a rendering of an arbitrary Go error. Recognize expected domain errors explicitly; make all other failures safely generic to the client and richly observable to the operator.

## Zone of proximal development notes

Amit already has a 401/403/204 handler outcome matrix and a control spy. Adding one fallback row is the smallest extension that completes the route’s basic error mapping without introducing new authentication or logging infrastructure.

## What comes next

- Define a small, consistent JSON error envelope if the API requires response bodies.
- Add a focused buffer-backed test that an unexpected control error creates a structured failure event while the client remains generic.
- Decide whether an operations read endpoint is needed, and give it a distinct read capability before exposing it.
