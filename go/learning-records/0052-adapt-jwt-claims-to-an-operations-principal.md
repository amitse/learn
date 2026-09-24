# Learning Record 0052 — Adapt JWT Claims to an Operations Principal

## Date
2026-09-24

## Lesson
0052 — Adapt JWT Claims to an Operations Principal

## What was taught

- Authentication middleware verifies a JWT and places typed claims into request context; it does not itself own application authorization policy.
- `PrincipalFromContext` is a small adapter that translates verified JWT claims into the application-owned `Principal` required by `LogLevelControl`.
- Server-owned policy maps a signed role (for example, `operator`) to `logs:write`; request headers and request bodies do not grant authority.
- An operations handler maps missing verified identity to HTTP 401, while `ErrForbidden` from the control becomes HTTP 403.
- The control keeps its Lesson 51 authorization guard, so another transport cannot bypass the operation-level check.
- Unit-test the adapter directly with context containing typed claims, then preserve the no-side-effects authorization test at the control boundary.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `principalFromClaims(req.claims)` | `auth.PrincipalFromContext(r.Context())` |
| service returns forbidden result | `errors.Is(err, ErrForbidden)` then HTTP 403 |
| controller maps auth failures to HTTP | `http.Handler` maps missing claims to 401 and forbidden to 403 |

## Key insight

A verified token is evidence of identity, not automatically a permission model. Translate it once at the application boundary into a transport-independent principal, then let the sensitive operation enforce that principal’s capability.

## Zone of proximal development notes

Amit has separate JWT middleware from Lesson 16 and an explicit, tested `Principal` authorization guard from Lesson 51. The smallest useful integration is one claims-to-principal adapter and a narrow HTTP status mapping, without moving authorization policy into a logger or trusting unverified request data.

## What comes next

- Table-test the operations handler’s 401, 403, and 204 outcomes with `httptest`.
- Decide whether the admin route is operationally necessary before exposing it outside a trusted network.
- Define token-issuer and role-change policy before relying on a role claim for production operations.
