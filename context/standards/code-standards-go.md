# Code Standards — Go

Go backend/systems conventions.

## Version

Target Go 1.23+. Use current features:

- **Enhanced `net/http` routing** (1.22+) — method and wildcard
  patterns built into the standard mux:
  ```go
  mux.HandleFunc("GET /users/{id}", getUser)
  mux.HandleFunc("POST /users", createUser)
  mux.HandleFunc("GET /files/{path...}", serveFile)
  ```
- **Range over integers** (1.22+) — `for i := range 10 { ... }`
- **Range over functions** (1.23+) — iterator protocol:
  ```go
  for k, v := range maps.All(m) { ... }
  for v := range slices.Values(s) { ... }
  ```
- **`slices`** and **`maps`** packages (stdlib) — use instead
  of hand-rolling sort, contains, filter operations
- **`log/slog`** — structured logging in stdlib, use instead
  of `log` or third-party loggers:
  ```go
  slog.Info("user created", "id", user.ID, "email", user.Email)
  ```
- **`cmp`** package — for generic comparison functions
- **Generic functions and types** — use where they eliminate
  real duplication, not for abstraction's sake

## General

- `gofmt` on every file — non-negotiable
- `go vet` and `staticcheck` pass with no warnings
- Modules for dependency management (`go.mod` / `go.sum`)

## Naming

- `PascalCase` for exported identifiers
- `camelCase` for unexported identifiers
- Short, clear names — `r` not `reader` in a 3-line scope,
  `userService` not `us` in a wide scope
- Packages: short, lowercase, singular (`user`, `auth`, `db`)
  — no `utils`, `helpers`, `common`
- Interfaces: verb or role name (`Reader`, `Validator`,
  `UserStore`) — not `IReader`
- No getters/setters prefix: `user.Name()` not `user.GetName()`

## Error Handling

- Return errors, don't panic — `panic` is for truly
  unrecoverable situations only
- Check every error — no `_` for error returns unless documented
- Wrap errors with context: `fmt.Errorf("loading user %s: %w", id, err)`
- Define sentinel errors or custom error types:
  ```go
  var ErrNotFound = errors.New("not found")

  type ValidationError struct {
      Field   string
      Message string
  }
  func (e *ValidationError) Error() string {
      return fmt.Sprintf("%s: %s", e.Field, e.Message)
  }
  ```
- Use `errors.Is` and `errors.As` — not string matching

## Functions

- Accept interfaces, return structs
- Small functions — one screen, one purpose
- Early returns for error/edge cases
- `context.Context` as first parameter for I/O or cancellable work

## Structs and Interfaces

- Keep interfaces small — one or two methods
- Define interfaces at the consumer, not the implementer
- Struct embedding for composition
- Zero values should be useful or obviously invalid

## Concurrency

- Don't start goroutines without a plan to stop them
- `context.Context` for cancellation
- Channels for communication, `sync.Mutex` for shared state
- `sync.WaitGroup` for fan-out/fan-in
- `errgroup.Group` for concurrent tasks with error collection
- Never leak goroutines — every goroutine needs an exit path

## Iterators and Collections

Use stdlib collection functions over hand-rolled loops:
```go
// yes — stdlib
idx := slices.Index(users, target)
sorted := slices.SortedFunc(users, compareByName)
has := slices.ContainsFunc(items, func(i Item) bool { return i.Active })

// no — manual loop for something the stdlib handles
found := -1
for i, u := range users {
    if u == target { found = i; break }
}
```

## Testing

- Table-driven tests as the default pattern
- Test file next to code: `user.go` → `user_test.go`
- Test names: `TestFunctionName_Scenario_ExpectedResult`
- `t.Helper()` in test helper functions
- Interfaces make mocking easy — no mocking frameworks needed
- Use `testing/fstest` and `net/http/httptest` from stdlib

## Logging

Use `log/slog` for all structured logging:
```go
logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
logger.Info("request handled",
    "method", r.Method,
    "path", r.URL.Path,
    "status", status,
    "duration", time.Since(start),
)
```

## Project Structure

- `cmd/` — entry points (one subdirectory per binary)
- `internal/` — private application code
- `pkg/` — only if building a reusable library
- Keep it flat until complexity demands folders

## Dependencies

- Standard library first — it covers most needs
- Minimize external dependencies
- Vet before adding: maintained, reasonable API, could you
  write it in 50 lines

## API (if building an HTTP service)

- Standard `net/http` mux with method patterns (1.22+) for
  most projects — `chi` or `gin` if you need middleware chaining
  beyond what stdlib provides. Document in `architecture.md`
- Middleware for cross-cutting concerns
- Consistent JSON response shapes
- `json.NewDecoder` with `DisallowUnknownFields` for input
- Graceful shutdown:
  ```go
  ctx, stop := signal.NotifyContext(context.Background(), os.Interrupt)
  defer stop()
  srv.Shutdown(ctx)
  ```
