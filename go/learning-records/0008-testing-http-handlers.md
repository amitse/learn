# Learning Record 0008 — Testing HTTP Handlers

## Date
2026-06-25

## Lesson
0009 — Testing HTTP Handlers in Go

## What was taught

- **`net/http/httptest`**: Go's standard library package for testing HTTP handlers. No external dependencies. `httptest.NewRecorder()` creates a fake `http.ResponseWriter` that records status, headers, and body. `httptest.NewRequest()` creates a fake `*http.Request` — neither involves a network connection.
- **Test files**: Live as `_test.go` files alongside the code they test. Discovered automatically by `go test`. No config required.
- **Test function signature**: `func TestFoo(t *testing.T)` — the `T` is the test controller for logging, failing, and stopping.
- **Table-driven tests**: Go's idiomatic pattern — define a slice of anonymous structs (each with inputs and expected outputs), loop over them, call `t.Run(name, func)` for each case. Produces named sub-tests like `TestFoo/valid_name`.
- **`t.Errorf` vs `t.Fatal`**: `Errorf` marks failure and continues; `Fatal`/`Fatalf` marks failure and stops the test immediately. Use `Fatal` when further assertions are meaningless (e.g., the JSON won't decode anyway).
- **Testing JSON responses**: Call `json.NewDecoder(w.Body).Decode(&resp)` to decode the recorder body into a struct; assert field values directly.
- **Useful flags**: `go test ./...` (all packages), `-v` (verbose), `-run TestName` (filter), `-cover` (coverage), `-count=1` (disable caching).

## TS → Go comparisons taught

| TypeScript (Jest/Vitest) | Go |
|---|---|
| `describe` / `it` blocks | `t.Run(name, func)` inside one test function |
| `expect(x).toBe(y)` | `if x != y { t.Errorf(...) }` |
| `supertest(app).get('/path')` | `httptest.NewRequest` + `httptest.NewRecorder` |
| Jest fails fast via `throw` | `t.Fatal(...)` / `t.FailNow()` |
| `jest --coverage` | `go test -cover ./...` |

## Key insight

Go's testing model is deliberately minimal: no describe blocks, no matchers, no assertion library — just `if` statements and `t.Errorf`. This feels verbose at first but makes test code as readable as any other Go code. The `httptest` package is the key unlock: you test HTTP handlers as ordinary functions, passing a recorder in place of a real connection. This keeps tests fast, deterministic, and zero-dependency.

## Zone of proximal development — next

Amit now has a complete, testable HTTP server in Go. The natural arc from here:
- **Project layout at scale** (Lesson 10): splitting handlers into sub-packages, shared types, internal vs external packages — how does a real Go repo scale beyond one `main.go`?
- Alternatively, a practical capstone: combining everything (modules, routing, JSON, middleware, error handling, tests) into one well-structured mini project.
