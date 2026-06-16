# Code Standards — C#

C# / .NET conventions. Framework-agnostic — specify ASP.NET Core,
console app, or other in `architecture.md`.

## Version

Target .NET 10 / C# 14 (current LTS, Nov 2025), or .NET 9 / C# 13.
Use current features:

- **Primary constructors** (C# 12) — for classes and structs,
  not just records:
  ```csharp
  public class UserService(IUserRepository repo, ILogger<UserService> logger)
  {
      public async Task<User> GetByIdAsync(string id, CancellationToken ct)
      {
          logger.LogInformation("Fetching user {Id}", id);
          return await repo.FindByIdAsync(id, ct)
              ?? throw new NotFoundException("User", id);
      }
  }
  ```
- **Collection expressions** (C# 12) — unified syntax:
  ```csharp
  List<int> nums = [1, 2, 3];
  int[] arr = [.. existingList, 4, 5]; // spread
  ReadOnlySpan<string> names = ["alice", "bob"];
  ```
- **Alias any type** (C# 12) — `using` for tuples, arrays, etc:
  ```csharp
  using Coordinate = (double Lat, double Lng);
  using UserList = System.Collections.Generic.List<User>;
  ```
- **Default lambda parameters** (C# 12):
  ```csharp
  var greet = (string name = "world") => $"Hello, {name}!";
  ```
- **`params` collections** (C# 13) — `params` works with
  `Span<T>`, `ReadOnlySpan<T>`, and any collection type
- **`Lock` object** (C# 13) — `System.Threading.Lock` for
  clearer locking intent:
  ```csharp
  private readonly Lock _lock = new();
  void Update() { lock (_lock) { ... } }
  ```
- **`field` keyword in properties** (preview in C# 13, stabilized in
  C# 14) — access the backing field directly in property accessors
- **`record`** — for immutable DTOs and value objects
- **`sealed`** — on classes not designed for inheritance
- **File-scoped namespaces** — `namespace X;` instead of wrapping braces
- **Raw string literals** — `"""..."""` for multi-line, no escaping
- **Pattern matching** — use in switch, is-expressions, list patterns

## General

- Nullable reference types enabled (`<Nullable>enable</Nullable>`)
- Treat warnings as errors in CI
- File-scoped namespaces everywhere
- `global using` for commonly used namespaces in `GlobalUsings.cs`

## Naming

- `PascalCase` for classes, interfaces, methods, properties,
  events, enums, namespaces, public fields
- `camelCase` for parameters, local variables
- `_camelCase` for private fields
- Interfaces: `I` prefix (`IUserRepository`, `ILogger`)
- Async methods: `Async` suffix (`GetUserAsync`, `SaveAsync`)
- Boolean properties/methods: `Is`, `Has`, `Can` prefix

## Architecture Pattern

- **Controllers** handle HTTP — parse, delegate, respond.
- **Services** contain business logic. No HTTP awareness.
- **Repositories** handle data access.
- DTOs (records) for API boundaries — entities don't leak.
- Dependency injection via constructor (primary constructors).

## Types and Structure

- `record` for immutable data:
  ```csharp
  public record UserDto(string Name, string Email);
  public record CreateUserRequest(string Name, string Email, Role Role = Role.User);
  ```
- `sealed` on classes not designed for inheritance
- `readonly struct` for small value types
- Prefer interfaces over abstract classes
- One class per file, file name matches class name
- Collection expressions for initialization:
  ```csharp
  List<string> tags = [.. existingTags, "new-tag"];
  Dictionary<string, int> counts = new() { ["a"] = 1 };
  ```

## Null Safety

- Nullable reference types enabled everywhere
- `?` for nullable, not-null by default
- No null from collection methods — return empty
- Null-conditional (`?.`), null-coalescing (`??`, `??=`)
- Pattern matching for null checks: `if (user is not null)`
- `ArgumentNullException.ThrowIfNull(param)` at public APIs
- `required` keyword on properties that must be set:
  ```csharp
  public class Config
  {
      public required string ConnectionString { get; init; }
      public int Port { get; init; } = 5432;
  }
  ```

## Error Handling

- Exceptions for exceptional conditions, not control flow
- Custom exception classes for domain errors
- Global exception handler middleware for HTTP APIs
- Never catch `Exception` broadly except at top-level
- Never swallow exceptions
- `ExceptionFilter` or middleware, not try-catch everywhere

## Async

- `async`/`await` for all I/O — no `.Result` or `.Wait()`
- Return `Task` or `Task<T>`, not `void` (except event handlers)
- `CancellationToken` on all async methods:
  ```csharp
  public async Task<User> GetByIdAsync(string id, CancellationToken ct = default)
  {
      return await repo.FindByIdAsync(id, ct);
  }
  ```
- `ConfigureAwait(false)` in library code only

## Dependency Injection

- Register in `Program.cs` or extension methods
- Primary constructors for injection — no `[Inject]`, no property injection
- Appropriate lifetimes: `Scoped` for request-bound,
  `Singleton` for stateless, `Transient` when cheap
- Depend on interfaces, not implementations
- Use keyed services (.NET 8+) when multiple implementations
  of the same interface exist

## LINQ

- Use LINQ for collection operations
- Method syntax for simple operations, query syntax if genuinely
  more readable
- Don't overchain — extract intermediate variables at 3-4 operations
- Materialize with `ToList()` or `ToArray()` when you need
  a concrete result
- Use `ToFrozenSet()` / `ToFrozenDictionary()` (.NET 8+) for
  read-heavy lookup collections

## Testing

- xUnit or NUnit — document in `architecture.md`
- Test names: `MethodName_Scenario_ExpectedResult`
- Moq or NSubstitute for mocking
- `[Theory]` with `[InlineData]` for parameterized tests
- `WebApplicationFactory` for integration tests

## File Organization

- One public class per file
- Namespace matches folder structure
- Group by feature (preferred) or by layer
- `Program.cs` — entry point and service registration
- `appsettings.json` — configuration (no secrets)
- `GlobalUsings.cs` — shared global usings

## Configuration

- Options pattern for typed config sections
- Secrets in environment variables or user secrets
- Validate at startup — fail fast on missing values
