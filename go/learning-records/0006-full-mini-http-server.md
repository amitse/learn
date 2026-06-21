# Learning Record 0006 — Full Mini HTTP Server

## Date
2026-06-21

## Lesson
0007 — Full Mini HTTP Server in Go

## What was taught

- **Integration lesson** — no new API surface; assembles lessons 3–6 into a single runnable binary
- **Project layout**: one package (`main`), split across `main.go`, `handlers.go`, `middleware.go`, `respond.go` — all share package scope, no intra-package imports needed
- **Shared respond helpers**: `writeJSON(w, status, data)` and `writeError(w, status, msg)` — centralise header-setting and encoding so handlers never manually touch `Content-Type`
- **Critical `return` rule**: after any `writeError(...)` call, execution continues unless you explicitly `return`. Forgetting causes "superfluous response.WriteHeader" — a common bug for TS developers used to `res.json()` terminating the Express handler chain
- **`log.Fatal(http.ListenAndServe(...))`** pattern — wraps ListenAndServe so a startup error (e.g. port in use) crashes with a logged message instead of silently failing
- **File-split ≠ module split**: splitting `main.go`/`handlers.go`/`respond.go` is for readability only; all symbols are in scope across all files in the package
- **`any` type alias** (Go 1.18+): `writeJSON` uses `any` as the data parameter type — equivalent to `interface{}`, cleaner syntax, analogous to `unknown` in TS but assignable from anything

## TS→Go comparisons taught

| Express/TypeScript | Go |
|---|---|
| `res.status(400).json({error:"..."})` — stops handler | `writeError(w, 400, "..."); return` — explicit return needed |
| `app.use(express.json())` — global body parse | No equivalent; each handler decodes its own body |
| `app.use(logger)` — global middleware | `handler := Logger(mux)` — wrap mux before ListenAndServe |
| File/module boundary | Directory/package boundary |
| `app.listen(8080)` never throws | `log.Fatal(http.ListenAndServe(...))` — crashes loudly on bind error |

## Key insight

This lesson's main value is demonstrating that Go's "verbosity" is **consistent and predictable**: every handler follows the same flow (decode → validate → respond), every error exits with `writeError + return`, and middleware composes at a single point. Once Amit writes this pattern once, the whole codebase becomes familiar — there are no framework-specific hooks or magic methods to remember.

## Zone of proximal development notes

All individual concepts were pre-learned in lessons 3–6. The non-obvious integration points:
1. The `return` after `writeError` (most likely source of bugs)
2. That splitting into files doesn't require imports — package = directory
3. `log.Fatal` wrapping ListenAndServe — why not just `http.ListenAndServe`?

## What comes next

Amit now has a complete, working HTTP server skeleton. Two natural next directions:
- **Error handling patterns** (Go 0008): idiomatic multi-return error handling, custom error types, sentinel errors — Go's `error` is `nil`-checked, not thrown
- **Rubik's Cube** is next in the alternating schedule — the Go track will continue with lesson 0008 on the following cycle
