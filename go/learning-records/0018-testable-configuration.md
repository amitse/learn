# Learning Record 0018 — Testable Configuration

## Date
2026-07-20

## Lesson
0019 — Test Configuration Without Touching the Environment

## What was taught

- A named function type, `type Lookup func(string) (string, bool)`, makes environment access an explicit dependency of `config.Load`.
- The function type deliberately has the same signature as `os.LookupEnv`, so production wiring can simply call `Load(os.LookupEnv)` (or a small `FromEnvironment` wrapper).
- The boolean preserves the distinction between an absent key and a present-but-empty value; this is important for required settings such as `JWT_SECRET`.
- A map-backed lookup function is a tiny in-memory test double: each table row supplies its own environment map and `Load` remains free of process-global state.
- Table-driven tests use `t.Run` to exercise valid defaults, missing required configuration, and malformed values independently.
- The loader remains responsible for parsing and validation; the lookup function only supplies strings. Its output is still typed `Config` or an error.

## TS → Go comparison

| TypeScript | Go |
|---|---|
| inject `(key) => process.env[key]` | inject `Lookup` / pass `os.LookupEnv` |
| test with a plain object fake | test with a map-backed function |
| avoid mutating global `process.env` | avoid mutating process environment |
| `string \| undefined` | `(string, bool)` |

## Key insight

A function is often the smallest useful dependency-injection boundary in Go. By depending on the behavior “look up a key” rather than the global OS environment, configuration stays simple in production and becomes deterministic to test.

## Zone of proximal development notes

Amit has already built a validated startup configuration boundary. This lesson adds the smallest possible seam for testing it, reusing the table-driven test pattern from Lesson 9 without introducing a framework or a large configuration library.

## What comes next

- **Assert configuration values:** extend the successful table row to assert the resulting address and duration, then add boundary cases such as port `0` and a zero timeout.
- **pgxpool:** move from `database/sql` to the native pgx pool.
- **testcontainers-go:** integration-test DB-backed handlers against ephemeral Postgres.
