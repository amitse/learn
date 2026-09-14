# Learning Record 0047 — Configure slog Level by Environment

## Date
2026-09-14

## Lesson
0047 — Configure `slog` Level by Environment

## What was taught

- The handler minimum level is deployment policy. Event call sites choose their truthful severity; configuration and logger construction decide which severities an environment emits.
- Store the setting as a typed `slog.Level` in `Config`, then pass it to `slog.HandlerOptions{Level: cfg.LogLevel}` when constructing the JSON logger.
- Raw `LOG_LEVEL` text is read only at the configuration boundary through the existing injected `Lookup` dependency; neither `newLogger` nor HTTP handlers should call `os.Getenv`.
- `slog.Level.UnmarshalText([]byte(raw))` parses the standard level names (`DEBUG`, `INFO`, `WARN`, `ERROR`) and produces an error for invalid text. This keeps the type's text policy with the type rather than duplicating a string-to-level map.
- Missing or empty `LOG_LEVEL` deliberately defaults to `slog.LevelInfo`. This must be written explicitly because the numeric zero value of `slog.Level` is `LevelDebug`, not the desired production default.
- Test the parser with a map-backed lookup for missing, valid, and invalid input. Separately capture logger output and assert that events below the configured threshold are absent while eligible events are present.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| typed `config.logLevel` passed to logger factory | typed `Config.LogLevel` passed to `newLogger` |
| logger transport configured with `level: "info"` | `HandlerOptions{Level: cfg.LogLevel}` |
| schema/parser rejects invalid environment text at startup | `level.UnmarshalText([]byte(raw))` returns an error during config loading |
| application chooses output threshold; calls still use `debug`/`info`/`warn` | handler filters records; calls still use `Debug`/`Info`/`Warn`/`Error` |

## Key insight

Severity is a fact supplied by the log call; the minimum emitted severity is deployment policy. Parse that policy once into a typed `slog.Level`, explicitly choose INFO when unset, and let the handler apply it consistently.

## Zone of proximal development notes

Amit already has an injected configuration lookup, JSON `slog` output, an explicit INFO threshold, and environment-controlled source locations. This lesson extends precisely that seam with one typed setting and reinforces a subtle Go zero-value concern without reopening lifecycle or request-logging topics.

## What comes next

- If a deployment genuinely needs a running process's log verbosity changed without restart, evaluate `slog.LevelVar` and define its ownership and concurrency policy.
- Before adding request logs, name the concrete operational question they answer and keep their event contract distinct from lifecycle events.
- Validate the accepted level names and chosen threshold policy with the team or log collector that operates the server.
