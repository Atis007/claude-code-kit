# Code Standards — C

C systems programming conventions.

## Version

Target C23 when compiler support allows, C11 as minimum
baseline. Specify in `architecture.md`.

C23 features to use when available:

- **`nullptr`** — use instead of `NULL` (proper null pointer
  constant with its own type `nullptr_t`)
- **`bool`, `true`, `false` as keywords** — no `#include <stdbool.h>`
  needed
- **`typeof` and `typeof_unqual`** — standard type deduction:
  ```c
  typeof(x) y = x; // y has the same type as x
  ```
- **`constexpr`** — true compile-time constants:
  ```c
  constexpr int MAX_USERS = 1024;
  constexpr double PI = 3.14159265358979;
  ```
- **Digit separators** — `1'000'000` for readability
- **`#embed`** — binary resource inclusion at compile time:
  ```c
  const unsigned char icon[] = {
      #embed "icon.png"
  };
  ```
- **`static_assert` without message** — `static_assert(sizeof(int) == 4);`
- **`auto`** for type inference (limited to simple cases)
- **Attributes**: `[[nodiscard]]`, `[[maybe_unused]]`,
  `[[deprecated]]`, `[[fallthrough]]`
- **Empty initializer** `{}` — zero-initializes any type
- **`unreachable()`** from `<stddef.h>` — marks code paths
  that should never execute

C11 baseline features (always use):

- `_Static_assert` / `static_assert`
- `_Alignof` / `alignof`
- Anonymous structs and unions
- `_Generic` for type-generic macros
- `<threads.h>` for portable threading (when available)
- `<stdatomic.h>` for atomic operations

## General

- Compile with warnings on: `-Wall -Wextra -Werror -Wpedantic`
- No undefined behavior — if you're not sure, it's UB
- Build system: Make or CMake — document in `architecture.md`

## Naming

- `snake_case` for functions, variables, typedefs, file names
- `UPPER_SNAKE_CASE` for macros, `constexpr` constants, enum values
- `PascalCase` or `snake_case_t` for struct/typedef types —
  pick one, be consistent
- Prefix public API with module name: `user_create()`,
  `user_destroy()`, `db_connect()`
- No single-letter names except loop indices and short scopes

## Memory Management

- Every `malloc` has a matching `free` — no exceptions
- Check every allocation: `malloc` can return `NULL`
- Clear ownership: document who allocates and who frees
- Create/destroy pattern for resource-owning structs:
  ```c
  User *user_create(const char *name);
  void user_destroy(User *user);
  ```
- Initialize all variables — use `{}` (C23) or `= {0}` (C11)
  for zero-initialization
- Set freed pointers to `nullptr` (C23) or `NULL` (C11)
- Prefer stack allocation for small, short-lived data
- Safe realloc pattern:
  ```c
  void *tmp = realloc(buf, new_size);
  if (!tmp) { /* handle error, buf still valid */ }
  buf = tmp;
  ```

## Error Handling

- Return error codes — `0` for success, negative for errors
- Define error codes as an enum:
  ```c
  enum {
      ERR_OK = 0,
      ERR_NOMEM = -1,
      ERR_INVALID = -2,
      ERR_IO = -3,
  };
  ```
- `[[nodiscard]]` on functions whose return value must be checked
- Check every return value
- Goto-cleanup for resource cleanup:
  ```c
  [[nodiscard]] int process(const char *path) {
      int result = ERR_OK;
      char *buf = malloc(size);
      if (!buf) { result = ERR_NOMEM; goto cleanup; }
      // ... work ...
  cleanup:
      free(buf);
      return result;
  }
  ```

## Functions

- Short — one screen, one purpose
- Parameters: inputs first, outputs last
- `const` on pointers you don't modify
- Maximum ~5 parameters — pass a struct beyond that
- `static` for file-local functions
- `[[nodiscard]]` on functions returning error codes or
  allocated resources

## Structs and Data

- Opaque types for encapsulation: forward-declare in headers,
  define in `.c`
- `typedef` for struct types
- Fixed-width types for sized data: `int32_t`, `uint8_t`
- `size_t` for sizes and counts
- `constexpr` (C23) or `#define` (C11) for compile-time constants

## Headers

- Every `.c` has a corresponding `.h` for public API
- Include guards:
  ```c
  #ifndef MODULE_NAME_H
  #define MODULE_NAME_H
  // ...
  #endif
  ```
- Minimal includes in headers — forward-declare where possible
- Self-contained: header compiles on its own

## Strings

- Always track buffer sizes — never assume enough space
- `snprintf` only — never `sprintf`
- `strncpy` or `strlcpy` — never unbounded `strcpy`
- For binary data, explicit `size` alongside the pointer

## File Organization

- `src/` — source files (`.c`)
- `include/` — public headers (`.h`)
- `tests/` — test files
- `build/` — build output (gitignored)
- `Makefile` or `CMakeLists.txt` at root

## Security

- Validate all external input — sizes, lengths, ranges
- Bounds-check every array access with external data
- No format string vulnerabilities: `printf("%s", input)`,
  never `printf(input)`
- `calloc` over `malloc` when zero-initialization matters
