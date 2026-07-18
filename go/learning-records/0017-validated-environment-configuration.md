# Learning Record 0017 — Validated Environment Configuration

## Date
2026-07-18

## Lesson
0018 — Validated Environment Configuration

## What was taught

- Configuration is a startup boundary: turn environment strings into one typed `Config` value before the server starts.
- `os.Getenv` returns only a string, so it cannot distinguish unset from explicitly empty values; `os.LookupEnv` returns `(string, bool)`.
- Use defaults only for safe operational values such as `PORT=8080` and `SHUTDOWN_TIMEOUT=5s`; required secrets such as `JWT_SECRET` must fail startup when absent or blank.
- `strconv.Atoi` parses the port once; `time.ParseDuration` parses shutdown timeouts such as `5s` once. Downstream code receives typed values rather than reparsing strings.
- `log.Fatal` at the composition root makes invalid configuration visible before an HTTP listener accepts traffic.
- Secrets belong in deployment-provided environment configuration and must not be committed to Git.

## TS → Go comparisons taught

| TypeScript | Go |
|---|---|
| `process.env.PORT ?? "8080"` | `getenv("PORT", "8080")` |
| `zod`/manual startup validation | `Load() (Config, error)` |
| `Number(process.env.PORT)` | `strconv.Atoi(port)` |
| `AbortSignal.timeout(...)` configuration | `time.ParseDuration("5s")` → `time.Duration` |
| `process.env.JWT_SECRET` presence check | `secret, ok := os.LookupEnv("JWT_SECRET")` |

## Key insight

Environment variables are untyped strings and should not leak through the application. Centralising defaults, parsing, and required-value checks in `config.Load` makes all later server wiring deterministic: authentication receives a non-empty secret and graceful shutdown receives a real `time.Duration`.

## Zone of proximal development notes

Amit already has JWT middleware and graceful shutdown. This lesson connects both to real deployment configuration without adding a framework, while reinforcing Go's explicit error-return pattern. The next practical step is testing the configuration loader without mutating the process environment.

## What comes next

- **Testable configuration:** inject an environment lookup function and write table-driven tests for defaults, malformed values, and missing secrets.
- **pgxpool:** move from `database/sql` to the native pgx pool.
- **testcontainers-go:** integration-test DB-backed handlers against ephemeral Postgres.
