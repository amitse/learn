# Learning Record 0053 — Table-Test Operations Auth Outcomes

## Date
2026-09-26

## Lesson
0053 — Table-Test Operations Auth Outcomes

## What was taught

- A handler-level authorization test begins after authentication middleware: it supplies either no claims context or already-verified typed claims.
- A table-driven `httptest` specifies the observable matrix: missing claims → 401 and no control call; verified viewer → 403; verified operator → 204.
- The 401 row must assert zero calls to prove the handler does not invoke the sensitive operation without a verified identity.
- The 403 row may assert one call because the authenticated principal reaches `LogLevelControl`, which remains the operation-level authorization owner.
- A narrow one-method control spy isolates status translation; Lesson 51's concrete control test remains responsible for proving a forbidden call has no state or audit side effects.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `it.each` request/outcome cases | `tests := []struct{...}` plus `t.Run` |
| test request with injected claims | `httptest.NewRequest(...).WithContext(ctx)` |
| response assertion | `httptest.NewRecorder()` then `w.Code` |

## Key insight

Authentication absence and authorization denial are different executable contracts. A three-row table makes their HTTP mapping and the sensitive-operation boundary independently visible: no identity never reaches the operation; a verified identity is evaluated by it.

## Zone of proximal development notes

Amit has a claims-to-principal adapter and explicit 401/403 mapping from Lesson 52, plus an independently guarded control from Lesson 51. The next smallest skill is a deterministic handler test that joins those seams without a real JWT, socket, or logger.

## What comes next

- Add a handler-table row mapping an unexpected control error to 500 without leaking its internal text.
- Decide whether the operations route should expose a current log-level read endpoint and give it its own capability.
- Before external exposure, define issuer, audience, expiry, and role-change policy for the JWT issuer.
