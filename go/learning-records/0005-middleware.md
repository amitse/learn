# Learning Record 0005 — Middleware

## Date
2026-06-18

## Lesson
0006 — Middleware in Go

## What was taught

- **Middleware signature**: `func(next http.Handler) http.Handler` — a function that takes a handler and returns a handler
- `http.Handler` is an **interface** with a single method: `ServeHTTP(ResponseWriter, *Request)`. Any type implementing that method satisfies the interface.
- `http.HandlerFunc` is a **type adapter** — it lets you use a plain `func(w, r)` wherever an `http.Handler` is expected, by satisfying the interface automatically
- **Before-after pattern**: code before `next.ServeHTTP(w, r)` runs before the handler; code after runs once the handler completes (e.g. logging duration)
- **Header ordering rule** reinforced: `w.Header().Set()` must come before any body write — middleware sets it safely because it runs before the handler
- **Global middleware**: wrap the entire mux — `handler := A(B(mux))`; outermost runs first on request arrival
- **Per-route middleware**: wrap a specific handler — `mux.Handle("/path", auth(http.HandlerFunc(h)))` — leaving other routes unaffected
- `mux.Handle()` vs `mux.HandleFunc()`: the former takes an `http.Handler` interface; the latter takes a plain function. `http.HandlerFunc(fn)` is the conversion bridge.

## TS→Go comparisons taught

| Express/TypeScript | Go |
|---|---|
| `app.use(logger)` | `handler := Logger(mux)` (wrap mux) |
| `app.use(express.json())` | `handler := JSONContentType(mux)` |
| `(req, res, next) => void` | `func(next http.Handler) http.Handler` |
| `app.get("/admin", auth, handler)` | `mux.Handle("/admin", auth(http.HandlerFunc(h)))` |
| Implicit `next()` call required | Explicit `next.ServeHTTP(w, r)` required |

## Key insight

Go has no global middleware registry — there is no `app.use()`. Middleware is explicit composition: you wrap handlers at the point of use. This makes dependencies visible in code rather than hidden in a configuration chain. The price is slight verbosity; the benefit is that any function can see exactly which middleware applies to a given handler without scanning the whole app startup.

## Zone of proximal development notes

Amit already understands the Express middleware mental model perfectly. The non-obvious Go parts:
1. The `http.Handler` interface vs `http.HandlerFunc` type distinction (easy to conflate)
2. That `http.HandlerFunc` is a *type adapter*, not a registry function
3. The nesting order for chaining (`A(B(mux))` — A runs first)
4. The need to explicitly convert `func(w, r)` → `http.HandlerFunc(fn)` when using `mux.Handle`

## What comes next

Lesson 0007: Full mini HTTP server — brings together routing (lesson 4), JSON (lesson 5), and middleware (lesson 6) into a single runnable Go binary with multiple routes, shared middleware, and proper error responses.
