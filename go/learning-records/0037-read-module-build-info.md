# Learning Record 0037 — Read Module Build Info

## Date
2026-08-25

## Lesson
0037 — Read Module Build Info

## What was taught

- `runtime/debug.ReadBuildInfo()` reads Go module/build metadata embedded in an executable when it is available.
- Its `(*debug.BuildInfo, bool)` result must be guarded with `ok` before code reads the returned pointer.
- `BuildInfo.Main.Version` can be `"(devel)"` in a local development build, so it is not a replacement for a deliberate release identity.
- A small `moduleVersion` selector can fall back to the existing linker-stamped `var version = "dev"` when build info is unavailable, blank, or development-only.
- Build metadata remains process-level operational data: call the selector from the startup lifecycle log at the application boundary, not from HTTP handlers, middleware, or response bodies.
- Explicit linker stamps for the release version and Git revision remain the stable CI-owned deploy contract; build info is optional diagnostic context.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| bundler/CI-injected `BUILD_VERSION` | linker-stamped `var version` with `-ldflags -X` |
| optional build metadata exposed by the runtime/toolchain | `debug.ReadBuildInfo()` returning `(*BuildInfo, bool)` |
| guard an optional value before property access | check `ok` before reading `info.Main.Version` |

## Key insight

The binary can carry toolchain-recorded build facts and CI-supplied release identity at the same time. Treat the explicit linker values as the intentional operational contract, and read module build info defensively as an optional diagnostic signal.

## Zone of proximal development notes

Amit can already stamp both a release version and exact Git revision into lifecycle logs. This lesson adds one standard-library API and one familiar Go `(value, ok)` guard, while preserving the established boundary and testable logging structure.

## What comes next

- Choose one precedence rule for a revision from linker metadata versus a `vcs.revision` build setting.
- Add a focused table-driven test for the module-version selector’s unavailable, development, and release cases.
- Consider structured `log/slog` fields after the lifecycle identity contract is stable.
