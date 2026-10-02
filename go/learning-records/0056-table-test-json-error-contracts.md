# Learning Record 0056 — Table-Test JSON Error Contracts

## Date
2026-10-02

## Lesson
0056 — Table-Test JSON Error Contracts

## What was taught

- A handler-level table should verify the selected public error contract, not only the response helper: missing identity maps to 401/`unauthorized`, a known authorization denial maps to 403/`forbidden`, and an unexpected control failure maps to 500/`internal server error`.
- Add a `wantError string` field to the existing outcome table; the empty value is reserved for the intentional 204 no-body success case.
- For error rows, assert `Content-Type: application/json`, decode `httptest.ResponseRecorder.Body` into `errorResponse`, and compare its `Error` field rather than raw JSON bytes.
- Supply a distinctive private value in the unexpected error and assert that it is absent from the response, proving the route did not serialize `err.Error()`.
- The test still uses the narrow control spy from Lesson 53; it verifies transport classification while application-level tests own authorization side effects.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `expect(await res.json()).toEqual({ error: message })` | decode `w.Body` into `errorResponse`, then compare `body.Error` |
| Jest `it.each` error matrix | `[]struct{ ... wantStatus; wantError }` plus `t.Run` |
| `expect(res.text()).not.toContain(privateValue)` | `strings.Contains(w.Body.String(), privateValue)` must be false |

## Key insight

An error envelope becomes a dependable client contract only when the handler’s classification is tested too. Decode the public response and separately require a known private cause to be absent; this protects meaning and security without coupling the test to JSON whitespace.

## Zone of proximal development notes

Amit already has a 401/403/204 handler outcome matrix, a tested unexpected-500 branch, and a reusable JSON error renderer. Extending that existing table to assert JSON bodies and a no-leak property is the smallest integration step that makes the full error boundary executable.

## What comes next

- Decide whether clients need a stable machine-readable error code alongside the display-safe `error` message.
- If so, extend the envelope deliberately and update its handler contract table before adding endpoints.
- Add a focused logger test that asserts the private cause is present in the structured server-side failure event while it remains absent from the client response.
