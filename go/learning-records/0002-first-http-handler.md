# Learning Record 0002 — First HTTP Handler

## Date
2026-06-12

## Lesson
0003 — Your First HTTP Handler

## What was taught

- `net/http` is a standard library package — no external dependency needed
- An HTTP handler is any function with the signature `func(w http.ResponseWriter, r *http.Request)`
- `http.ResponseWriter` (interface): used to write the response — status code, headers, body
- `*http.Request` (struct pointer): contains everything about the incoming request — method, URL, headers, body
- `http.HandleFunc("/path", handlerFn)` registers a handler on the DefaultServeMux
- `http.ListenAndServe(":8080", nil)` starts the server; `nil` means "use DefaultServeMux"
- `w.WriteHeader(http.StatusNotFound)` sets the status code — **must be called before any body write**
- `w.Header().Set("Key", "Value")` sets response headers — must be called before `WriteHeader` or first body write
- `r.Method` — the HTTP verb ("GET", "POST", etc.)
- `r.URL.Path` — the URL path string ("/users/42")
- `r.URL.Query().Get("param")` — a query parameter value
- `r.Header.Get("Header-Name")` — a request header value
- `fmt.Fprintln(w, ...)` writes to the response body (w satisfies io.Writer)

## TS→Go comparisons taught

| Express (TS) | Go (net/http) |
|---|---|
| `app.get('/', handler)` | `http.HandleFunc("/", handler)` |
| `req.method` | `r.Method` |
| `req.path` | `r.URL.Path` |
| `res.status(404).send(...)` | `w.WriteHeader(404)` then write body |
| `res.set('Content-Type', ...)` | `w.Header().Set(...)` |

## Key insight

Unlike Express, Go's response writer is **sequential and one-shot**: headers → WriteHeader → body, in that order. Once you call `fmt.Fprintln(w, ...)`, Go has already sent a 200 if you hadn't called `WriteHeader` first. This ordering constraint is a common source of bugs for TS developers porting to Go.

## Zone of proximal development notes

Amit knows Express well, so the handler shape maps cleanly. The main non-obvious points are:
1. `nil` in `ListenAndServe` (why it works without an explicit mux)
2. The header/status/body ordering constraint

## What comes next

Lesson 0004: Routing & Path Parameters — replace `DefaultServeMux` with a custom `http.ServeMux`, understand prefix vs exact matching, and extract path segments (e.g. `/users/{id}`). Builds directly on the handler and registration patterns from this lesson.
