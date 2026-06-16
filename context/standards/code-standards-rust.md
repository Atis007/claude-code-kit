# Code Standards — Rust

Rust systems / backend conventions.

## Version

Target Rust 2024 edition (1.85+). Use current stable features:

- **Async fn in traits** (1.75+) — no `async-trait` crate needed
  for most cases:
  ```rust
  trait UserStore {
      async fn find_by_id(&self, id: &str) -> Result<User>;
      async fn save(&self, user: &User) -> Result<()>;
  }
  ```
- **`impl Trait` in return position in traits** (1.75+)
- **`let`-`else`** (1.65+) — early return on pattern mismatch:
  ```rust
  let Some(user) = users.get(id) else {
      return Err(AppError::NotFound("user", id));
  };
  ```
- **C-string literals** (1.77+) — `c"hello"` for FFI
- **`OnceLock` / `OnceCell`** (1.70+) and **`LazyLock` / `LazyCell`**
  (1.80+) — one-time-init and lazy statics in stdlib:
  ```rust
  static CONFIG: LazyLock<Config> = LazyLock::new(|| {
      Config::from_env().expect("valid config")
  });
  ```
- **`offset_of!`** (1.77+) — struct field offsets in stdlib
- **Precise capturing** (`use<>`) (1.82+) — control what
  lifetimes `impl Trait` captures
- **`gen` blocks** (nightly/unstable) — follow stabilization
  for generator-based iterators

## General

- `cargo clippy` with warnings denied — `#![deny(clippy::all)]`
- `cargo fmt` on every file
- `rust-analyzer` for IDE support
- Minimum supported Rust version (MSRV) documented in `Cargo.toml`

## Naming

- `snake_case` for functions, methods, variables, modules, crates
- `PascalCase` (UpperCamelCase) for types, traits, enum variants
- `SCREAMING_SNAKE_CASE` for statics and constants
- `_leading_underscore` for intentionally unused variables
- Short lifetimes: `'a`, `'b` — descriptive only when multiple
  lifetimes need distinction (`'input`, `'output`)
- Crate names: lowercase with hyphens (`my-crate`), module
  names with underscores (`my_module`)

## Ownership and Borrowing

- Prefer borrowing (`&T`, `&mut T`) over ownership when the
  function doesn't need to own the data
- Use `Clone` explicitly when ownership transfer is needed —
  don't hide copies
- `Cow<'_, str>` when a function sometimes needs to allocate
  and sometimes doesn't
- Return owned types from constructors and builders
- `&str` in function parameters, `String` in struct fields
  (usually — `Cow` or generics when performance matters)
- Avoid `Rc`/`Arc` until you actually need shared ownership —
  restructure to avoid it first

## Error Handling

- Use `Result<T, E>` for all fallible operations
- `?` operator for error propagation — don't match just to re-wrap
- Define error types with `thiserror`:
  ```rust
  #[derive(Debug, thiserror::Error)]
  enum AppError {
      #[error("not found: {0} {1}")]
      NotFound(&'static str, String),
      #[error("validation: {0}")]
      Validation(String),
      #[error("database: {0}")]
      Database(#[from] sqlx::Error),
  }
  ```
- `anyhow::Result` for application code where you don't need
  to match on error variants (scripts, CLI tools, top-level)
- `thiserror` for library code and domain errors callers match on
- Never `unwrap()` or `expect()` in library code — only in
  tests and truly impossible cases with a clear message
- `let`-`else` for early returns on `Option`/`Result`:
  ```rust
  let Ok(parsed) = input.parse::<i64>() else {
      return Err(AppError::Validation("invalid number".into()));
  };
  ```

## Types and Data

- `struct` for domain entities — derive `Debug` at minimum
- `enum` for state machines and variants — exhaustive matching
  is a feature, use it
- Newtypes for domain concepts: `struct UserId(String)` —
  prevents mixing up string parameters
- `#[derive(Debug, Clone, PartialEq)]` as a baseline for
  data types — add `Eq`, `Hash`, `Serialize`, `Deserialize`
  as needed
- `#[non_exhaustive]` on public enums and structs that may
  gain variants/fields

## Traits

- Small, focused traits — one method is fine
- `async fn` in traits directly (1.75+) — no `#[async_trait]`
  macro unless you need `dyn` dispatch
- `impl Trait` in argument position for generic flexibility
  without naming the type:
  ```rust
  fn process(items: impl Iterator<Item = &str>) -> Vec<String>
  ```
- Default implementations when there's a sensible default
- Derive macros over manual impls when the derived behavior
  is correct

## Modules and Visibility

- `pub` only for the public API — everything else is private
- `pub(crate)` for crate-internal sharing
- Module structure mirrors the conceptual architecture:
  ```
  src/
  ├── main.rs or lib.rs
  ├── config.rs
  ├── error.rs
  ├── routes/
  │   ├── mod.rs
  │   └── users.rs
  ├── services/
  │   ├── mod.rs
  │   └── user_service.rs
  ├── repositories/
  │   ├── mod.rs
  │   └── user_repo.rs
  └── models/
      ├── mod.rs
      └── user.rs
  ```
- Re-export public types from `mod.rs` or `lib.rs` for clean API

## Async

- `tokio` as the async runtime for most projects — document
  in `architecture.md`
- `async fn` in traits directly for static dispatch
- Use `tokio::spawn` for concurrent tasks, `tokio::select!` for
  racing futures
- Structured concurrency: `JoinSet` for managing multiple tasks
- Cancel safety: document whether async functions are cancel-safe
- Avoid `block_on` inside async contexts — it deadlocks

## Collections and Iterators

- Iterator chains over manual loops:
  ```rust
  let active_emails: Vec<&str> = users.iter()
      .filter(|u| u.is_active)
      .map(|u| u.email.as_str())
      .collect();
  ```
- `collect()` with type annotation — let the compiler know
  what you're collecting into
- `HashMap` / `BTreeMap` — use `BTreeMap` when you need ordering
- Pre-allocate with `Vec::with_capacity` when size is known

## Testing

- `#[cfg(test)]` module in each source file for unit tests
- `tests/` directory for integration tests
- Test names: `test_function_scenario_expected`
- Use `assert_eq!`, `assert_ne!`, `assert!(matches!(..))`
- `#[should_panic(expected = "...")]` for panic tests
- Mock traits with manual test implementations or `mockall`

## Dependencies

- Minimize dependencies — each one is compile time and attack surface
- Check with `cargo audit` regularly
- Feature-gate optional dependencies in library crates
- Pin versions in `Cargo.lock` (committed for binaries,
  not for libraries)

## Unsafe

- Avoid `unsafe` unless absolutely necessary
- Every `unsafe` block must have a `// SAFETY:` comment
  explaining why the invariants are upheld
- Encapsulate unsafe in safe abstractions — never expose
  unsafe to callers
- Prefer safe alternatives even if slightly slower

## API (if building an HTTP service)

- `axum` (preferred) or `actix-web` — document in `architecture.md`
- `sqlx` for compile-time checked SQL queries
- `serde` for serialization/deserialization
- `tower` middleware for cross-cutting concerns
- Consistent JSON response shapes
- Proper HTTP status codes
- Graceful shutdown with `tokio::signal`
