# Learning Record 0035 — Stamp a Build Version into Lifecycle Logs

## Date
2026-08-20

## Lesson
0035 — Stamp a Build Version into Lifecycle Logs

## What was taught

- Process build metadata belongs at the application lifecycle boundary, beside start, stop, and failure logs—not in HTTP handlers or request context.
- A package-level `var version = "dev"` provides an honest local-development fallback.
- Go's linker can set a string variable at release build time with `go build -ldflags "-X main.version=v1.4.0"`.
- `-X` targets `importpath.name`; for a variable in another package, use that package's full import path rather than `main.version`.
- The target must be a string variable, not a `const`; the linker changes the variable value while producing the binary.
- A buffer-backed logger test should assert the default version in the normal lifecycle message. CI should run a separately stamped artifact once to verify the build hand-off.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| bundler-injected `BUILD_VERSION` | linker-set `var version` via `-ldflags -X` |
| `logger.info({ version }, "server started")` | `logger.Printf("HTTP server starting version=%s", version)` |
| build-time environment replacement | link-time variable replacement |

## Key insight

A version is a property of the binary, not a request. Giving it one default in source and stamping it during the release build makes every application-boundary lifecycle event traceable to a deployed artifact while keeping HTTP code free of build concerns.

## Zone of proximal development notes

Amit has a testable application wrapper that returns failures, preserves successful `nil` results, and logs normal lifecycle completion. This is the smallest operational extension: add static build identity to those existing messages without adding infrastructure, logging frameworks, or request-level state.

## What comes next

- Stamp a Git commit SHA alongside a human-readable release version in CI.
- Compare a linker-stamped release version with build settings exposed by `runtime/debug.ReadBuildInfo`.
- Decide whether a version belongs only in logs or also in a narrowly scoped readiness endpoint.
