# Learning Record 0040 — Structured Startup Identity with slog

## Date
2026-08-31

## Lesson
0040 — Structured Startup Identity with `slog`

## What was taught

- `log/slog` represents a log event as a stable message plus named attributes, rather than only a formatted string.
- At the application boundary, `logger.Info("HTTP server starting", slog.String("version", moduleVersion()), slog.String("revision", revisionForLog()))` records process identity once, without leaking it into request handlers.
- `slog.New(slog.NewTextHandler(os.Stderr, nil))` creates a production-friendly text logger; a JSON handler is an alternative presentation for log collectors.
- An injected `*slog.Logger` keeps the lifecycle wrapper testable, just as the prior injected standard logger did.
- A buffer-backed `slog.NewTextHandler` lets a focused test assert the stable startup message and `version`/`revision` fields without a server, global logger, or runtime build-info dependency.
- The Lesson 39 pure selector table remains separate: it specifies which revision wins; the new test specifies that the chosen identity is emitted as structured fields.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `logger.info({ version, revision }, "HTTP server starting")` | `logger.Info("HTTP server starting", slog.String("version", version), slog.String("revision", revision))` |
| structured logger transport decides JSON/text | `slog.Handler` decides text/JSON rendering |
| injected logger writes to test sink | injected `*slog.Logger` with `TextHandler(&bytes.Buffer{}, ...)` |

## Key insight

Build identity is useful operational data only when it is both truthful and queryable. Select it with the existing pure policy, then attach it as named attributes to one application-boundary lifecycle event.

## Zone of proximal development notes

Amit already has a deterministic revision precedence policy and a testable lifecycle wrapper with text logging. Migrating only the startup event to `slog` adds structured presentation while reusing the same separation of policy, process boundary, and buffer-backed test technique.

## What comes next

- Test an `slog` failure event while preserving `errors.Is` on the returned error.
- Decide whether text or JSON is the production handler based on the deployment log collector.
- Add an intentionally narrow readiness endpoint only if operators need identity over HTTP; keep it separate from normal handlers.
