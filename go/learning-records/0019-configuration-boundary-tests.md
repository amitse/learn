# Learning Record 0019 — Configuration Boundary Tests

## Date
2026-07-22

## Lesson
0020 — Test Configuration Boundaries, Not Just Errors

## What was taught

- A successful `Load` call should be tested for its typed output, not only for `err == nil`.
- In a table-driven test, each successful case carries an expected `Config`; compare its `Addr`, `JWTSecret`, and `ShutdownTimeout` fields directly.
- Use `t.Fatal`/`t.Fatalf` when a setup or unexpected error makes later assertions meaningless; use `t.Errorf` for independent field mismatches.
- Once an error case correctly produces an error, `return` from that subtest before attempting output assertions.
- Port `0` is valid: binding `:0` asks the operating system to choose an available ephemeral port. Configuration validation should accept the inclusive range `0..65535`.
- A shutdown timeout must remain strictly positive: `0s` leaves no time for `http.Server.Shutdown` to drain active requests.

## TS → Go comparison

| TypeScript | Go |
|---|---|
| `expect(config).toEqual(expected)` | direct field comparisons with `t.Errorf` |
| successful parse plus object assertion | `err == nil` plus typed `Config` assertions |
| test server on a random port | listen on `:0` and inspect its assigned address |
| return after expected rejection | `if tt.wantErr { return }` |

## Key insight

Configuration tests describe a startup contract. A test that merely accepts success cannot detect a wrong default, a unit conversion bug, or an incorrectly formatted listener address; asserting the final `Config` verifies exactly what the HTTP server will consume.

## Zone of proximal development notes

Amit already has a validated loader and an injected lookup dependency. This lesson turns those foundations into precise, deterministic tests and refines the port rule to support ephemeral ports—useful for the next step of testing application wiring without a port collision.

## What comes next

- **Server wiring tests:** build a listener from `Config.Addr`, use `:0`, and inspect the assigned address without relying on port 8080.
- **pgxpool:** move from `database/sql` to the native pgx connection pool.
- **testcontainers-go:** integration-test DB-backed handlers against ephemeral Postgres.
