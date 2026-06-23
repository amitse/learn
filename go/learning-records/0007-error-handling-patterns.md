# Learning Record 0007 — Error Handling Patterns

## Date
2026-06-23

## Lesson
0008 — Error Handling Patterns in Go

## What was taught

- **Error as value**: Go has no exceptions; functions signal failure via a second `(T, error)` return value. If no error, the error value is `nil`. This is the most fundamental difference from TypeScript.
- **Creating errors**: Two standard tools — `errors.New("message")` for static messages, `fmt.Errorf("context: %w", err)` for dynamic messages with wrapped cause.
- **Sentinel errors**: Package-level `var ErrXxx = errors.New(...)` are the Go equivalent of known error codes. Named with `Err` prefix by convention.
- **Wrapping with `%w`**: `fmt.Errorf("context: %w", err)` preserves the original error inside the new one. Using `%v` would convert it to a string and lose the chain.
- **`errors.Is`**: Walks the full error chain to match a specific *value* (sentinel). Replaces `==` when errors may be wrapped.
- **`errors.As`**: Walks the full error chain to match and extract a specific *type* (custom struct). Pass a pointer-to-pointer target.
- **Custom error types**: Any struct implementing `Error() string` is an `error`. Used to carry structured data (e.g., HTTP status codes) alongside error messages.
- **HTTP handler pattern**: Service functions return typed `*AppError`; handler uses `errors.As` to extract the code and call `writeError(w, appErr.Code, appErr.Message)` — clean separation of business logic from HTTP concerns.
- **`return` after `writeError` reminder**: Reinforced from lesson 7 — always `return` after writing an error response.

## TS→Go comparisons taught

| TypeScript | Go |
|---|---|
| `try { } catch (err) { }` | `result, err := fn(); if err != nil { }` |
| `throw new Error("msg")` | `return errors.New("msg")` or `return fmt.Errorf("ctx: %w", err)` |
| `err instanceof NotFoundError` | `errors.As(err, &target)` |
| `err === ERR_NOT_FOUND` | `errors.Is(err, ErrNotFound)` |
| Custom error class | Struct implementing `Error() string` |

## Key insight

Go's error handling feels verbose at first, but it's **linear and visible** — every possible failure is declared in the function signature and handled inline. There are no hidden throws, no async error boundaries, no uncaught promise rejections. Once Amit internalises the `if err != nil { return ... }` reflex, debugging becomes straightforward: follow the error up the call stack, one return value at a time.

## Zone of proximal development notes

Amit has a complete working HTTP server (lesson 7). The missing piece was: what happens when things go wrong? This lesson connects error handling directly to the HTTP layer via custom error types and `errors.As`, giving the server production-grade error responses.

## What comes next

- **Testing HTTP handlers** (lesson 9): `net/http/httptest` — writing `_test.go` files, `httptest.NewRecorder`, table-driven tests. Natural next step now that the server structure and error patterns are solid.
- Alternatively: **project layout at scale** — splitting handlers into sub-packages, shared error types across packages.
