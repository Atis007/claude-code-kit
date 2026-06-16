# Code Standards — Java

Java backend conventions. Framework-agnostic — specify
Spring Boot, Quarkus, or plain Java in `architecture.md`.

## Version

Target Java 21+ (LTS). The current LTS is Java 25 (Sept 2025);
21 remains a supported LTS baseline. Use current features:

- **Virtual threads** (21) — lightweight threads for concurrent
  I/O. Use `Executors.newVirtualThreadPerTaskExecutor()` for
  I/O-bound work instead of platform thread pools:
  ```java
  try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
      var futures = users.stream()
          .map(u -> executor.submit(() -> enrichUser(u)))
          .toList();
  }
  ```
- **Pattern matching for switch** (21) — exhaustive, type-safe:
  ```java
  return switch (shape) {
      case Circle c -> Math.PI * c.radius() * c.radius();
      case Rectangle r -> r.width() * r.height();
      case Triangle t -> 0.5 * t.base() * t.height();
  };
  ```
- **Record patterns** (21) — destructure in switch and instanceof:
  ```java
  if (result instanceof Success(var data, var timestamp)) {
      process(data);
  }
  ```
- **Sealed classes/interfaces** (17) — closed type hierarchies:
  ```java
  sealed interface Result<T> permits Success, Failure {}
  record Success<T>(T data) implements Result<T> {}
  record Failure<T>(String error) implements Result<T> {}
  ```
- **Records** (16) — immutable data carriers for DTOs:
  ```java
  public record UserDto(String name, String email, Role role) {}
  ```
- **Text blocks** (15) — multi-line strings with `"""`
- **`var`** (10) — local variable type inference for obvious types
- **Sequenced collections** (21) — `getFirst()`, `getLast()`,
  `reversed()` on List, Set, Map

Note: string templates (previewed in 21–22) were **withdrawn** and are
not in the language as of JDK 23+ — do not rely on them.

## General

- Maven or Gradle — pick one, document in `architecture.md`
- Compiler warnings treated as errors in CI
- No wildcard imports — explicit imports only
- Use `var` when the type is obvious from the right side:
  ```java
  var users = userRepository.findAll(); // obvious: List<User>
  UserService service = new UserService(repo); // keep type when non-obvious
  ```

## Naming

- `PascalCase` for classes, interfaces, records, enums
- `camelCase` for methods, variables, parameters
- `UPPER_SNAKE_CASE` for constants (`static final`)
- Packages: lowercase, reverse domain (`com.project.module`)
- Interfaces: noun or adjective — no `I` prefix
- Implementations: descriptive (`JpaUserRepository`) — not `Impl`
- Boolean methods: `is`, `has`, `can` prefix

## Architecture Pattern

- **Controllers** handle HTTP — parse, validate, delegate, respond.
- **Services** contain business logic. No HTTP awareness.
- **Repositories** handle data access.
- DTOs for API boundaries — entities don't leak into responses.
  Use records for DTOs.
- One controller per resource, one service per domain concept.

## Types and Structure

- `record` for immutable data (DTOs, value objects, events)
- `sealed` for closed type hierarchies — enables exhaustive
  pattern matching in switch
- `enum` for fixed value sets — with behavior when appropriate
- `Optional` for return types that may be absent — never for
  parameters or fields
- Prefer composition over inheritance

## Pattern Matching

Use pattern matching over instanceof chains:
```java
// yes — pattern matching switch
return switch (event) {
    case OrderCreated e -> handleNew(e.orderId());
    case OrderPaid e -> handlePayment(e.orderId(), e.amount());
    case OrderCancelled e -> handleCancel(e.orderId(), e.reason());
};

// no — instanceof chain
if (event instanceof OrderCreated) {
    handleNew(((OrderCreated) event).orderId());
} else if (event instanceof OrderPaid) { ... }
```

## Error Handling

- Checked exceptions for recoverable conditions the caller
  must handle
- Unchecked exceptions for programming errors and unrecoverable
  failures
- Custom exception classes per domain error
- Global exception handler maps exceptions to HTTP responses
- Never catch `Exception` or `Throwable` broadly
- Never swallow exceptions
- Log at the catch site with context

## Null Safety

- Minimize null — use `Optional`, empty collections, sentinel values
- `@Nullable` / `@NonNull` on public API boundaries
- `Objects.requireNonNull` at method entry for required params
- Never return null from collection-returning methods

## Concurrency

Prefer virtual threads for I/O-bound work:
```java
// HTTP handler with virtual threads (Spring Boot 3.2+)
// spring.threads.virtual.enabled=true in application.properties

// Manual virtual thread usage
Thread.startVirtualThread(() -> processInBackground(item));
```

Use platform threads only for CPU-bound parallel computation.
Don't use `synchronized` in virtual thread code — use
`ReentrantLock` instead.

## Streams and Collections

Use streams for collection processing, but don't overchain:
```java
// good — clear pipeline
var activeEmails = users.stream()
    .filter(User::isActive)
    .map(User::email)
    .toList();

// too much — extract intermediate steps
var result = items.stream()
    .filter(...)
    .map(...)
    .flatMap(...)
    .collect(groupingBy(..., mapping(..., reducing(...))));
// break this into named steps
```

Use sequenced collection methods (21+):
```java
var first = list.getFirst();
var last = list.getLast();
var reversed = list.reversed();
```

## Testing

- JUnit 5 for tests
- Test names: `methodName_scenario_expectedResult`
- `@ParameterizedTest` for table-driven patterns
- Mockito for mocking — mock interfaces, not classes
- Test behavior, not implementation
- One assertion concept per test

## Dependencies

- Minimize dependencies
- Use framework built-ins before adding libraries
- Pin dependency versions — no dynamic ranges

## File Organization

- Package by feature for larger projects, by layer for smaller:
  ```
  com.project.user/
  ├── UserController.java
  ├── UserService.java
  ├── UserRepository.java
  └── UserDto.java
  ```
- Document the choice in `architecture.md`

## API (if building an HTTP service)

- Consistent JSON response shapes
- Proper HTTP status codes
- Input validation at controller level
- Auth before any mutation
- Pagination for list endpoints
