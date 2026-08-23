# Learning Record 0036 — Stamp a Git Commit into Lifecycle Logs

## Date
2026-08-23

## Lesson
0036 — Stamp a Git Commit into Lifecycle Logs

## What was taught

- A release version and a Git commit SHA answer different operational questions: the version is a readable release label; the SHA identifies the exact source revision that produced a binary.
- Both are static process metadata and belong in application-boundary lifecycle logs, not HTTP handlers, request context, or response types.
- Declare truthful source defaults with `var version = "dev"` and `var revision = "none"`; the linker can override each at release build time.
- In CI, resolve the checked-out revision once with `git rev-parse HEAD`, then pass both values using Go linker flags: `-ldflags "-X main.version=$VERSION -X main.revision=$REVISION"`.
- `-X` modifies string variables, not constants. A variable outside `package main` must use its full import path in the linker target.
- Unit tests should assert the local fallback identity through an isolated buffer-backed logger. A separate CI smoke check should run the stamped artifact and assert its startup identity.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| CI-injected `BUILD_VERSION` and `GIT_SHA` | linker-stamped `var version` and `var revision` |
| `logger.info({ version, revision }, "server starting")` | `logger.Printf("... version=%s revision=%s", version, revision)` |
| build environment identifies the deployment | link-time values identify the compiled binary |

## Key insight

A friendly release label is useful to people, but a commit SHA makes an artifact traceable to exact source. Logging both once at the application boundary gives operators deploy identity without leaking build concerns into request handling.

## Zone of proximal development notes

Amit already knows how Go's linker stamps a single build version and how the application boundary owns lifecycle logging. Adding one more static string, sourced deterministically from CI, is the smallest operational extension that makes a lifecycle event traceable to source without introducing a logging framework or endpoint.

## What comes next

- Compare linker-stamped metadata with `runtime/debug.ReadBuildInfo` and decide which source of identity the binary should prefer.
- Decide whether build identity should be exposed through a narrowly scoped readiness endpoint or remain logs-only.
- Add structured `log/slog` fields only if the plain lifecycle event contract needs machine-readable querying.
