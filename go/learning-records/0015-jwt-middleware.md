# Learning Record 0015 — JWT Middleware

## Date
2026-07-14

## Lesson
0016 — JWT Middleware

## What was taught

- **JWT structure**: three base64url-encoded parts (header.payload.signature) — server verifies signature, never stores the token.
- **golang-jwt/jwt v5**: community-standard library replacing the archived `dgrijalva/jwt-go`.
- **Claims struct**: embed `jwt.RegisteredClaims` to get automatic `exp`/`iat`/`nbf` validation; add custom fields alongside.
- **SignToken helper**: `jwt.NewWithClaims(jwt.SigningMethodHS256, claims)` + `token.SignedString([]byte(secret))`.
- **Keyfunc pattern**: secret is returned from a function, enabling future swap to asymmetric keys without changing call sites.
- **AuthMiddleware factory**: returns `func(http.Handler) http.Handler` — same middleware interface as Lesson 6.
- **Context key type**: unexported named type (`type contextKey string`) prevents collision with third-party packages when using `context.WithValue`.
- **r.WithContext(ctx)**: returns a shallow copy of the request with the new context — idiomatic; avoids data races.
- **Type-asserting from context**: always use two-return form `v, ok := ctx.Value(k).(T)` to avoid panics.
- **Status codes**: 401 Unauthorized for missing/invalid/expired tokens; 403 Forbidden for authenticated-but-not-allowed.

## TS → Go comparisons taught

| TypeScript (jsonwebtoken) | Go (golang-jwt/jwt) |
|---|---|
| `jwt.verify(token, SECRET)` | `jwt.ParseWithClaims(raw, &Claims{}, keyfunc)` |
| Attach to `req.user` | Attach to `context.WithValue(ctx, key, claims)` |
| Plain object payload | Typed `Claims` struct with embedded `RegisteredClaims` |
| Secret as second arg | Secret returned from keyfunc function |

## Key insight

The context key pattern (unexported custom type) is idiomatic Go for avoiding key collisions — it's more correct than using a plain string even within a single codebase. The factory middleware signature (`func(http.Handler) http.Handler`) makes JWT auth compose with any other middleware without special casing.

## Zone of proximal development notes

Amit now has auth integrated into the middleware chain. Natural next lessons:
- **pgxpool** — replace `database/sql` with the native pgx connection pool
- **testcontainers-go** — integration-test DB handlers with real ephemeral Postgres
- **Graceful shutdown** — drain in-flight requests on SIGTERM
