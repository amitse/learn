# Learning Record 0001 — Go Modules & Project Structure

## Date
2026-06-10

## Lesson
0002 — Go Modules & Project Structure

## What was taught

- `go mod init <module-path>` initializes a module and creates `go.mod`
- `go.mod` is Go's equivalent of `package.json` — it declares the module name, Go version, and dependencies
- `go.sum` is the lockfile equivalent — auto-managed, should be committed but never edited by hand
- The standard library (net/http, fmt, encoding/json, etc.) is bundled with the toolchain — no install step needed
- **Packages are directories, not files** — all `.go` files in a directory share one package name
- **Exported = capitalized** — a capital first letter is the only visibility modifier; no `export` keyword
- Go imports always use the full module path (e.g. `github.com/yourname/api/handlers`), never relative paths
- `go run .` — compile and run immediately (dev workflow)
- `go build .` — produces a single, fully self-contained static binary with no runtime dependency

## Key insight

The TS mental model of "file = module" breaks in Go. In Go, **directory = package**. Multiple files in the same directory share the same package scope. This is why you can call `Greet()` from `main.go` even though it's defined in `greet.go` — they're in the same package.

## Zone of proximal development notes

Amit knows TS modules well, so the package-as-directory concept is the main friction point. The capitalization-as-export rule is also non-obvious and likely to cause compiler errors until internalized.

## What comes next

Lesson 0003: First HTTP Handler — using `net/http`, writing a `HandlerFunc`, registering it with `http.HandleFunc`, and running `http.ListenAndServe`. Builds directly on the module/package concepts from this lesson.
