# Architecture Context

## Stack

| Layer      | Technology               | Role                      |
| ---------- | ------------------------ | ------------------------- |
| Language   | C11 / C17               | Systems programming       |
| Build      | [Make / CMake]           | Build system              |
| Platform   | [Linux / cross-platform] | Target OS                 |
| Libraries  | [e.g. libuv, sqlite3]   | External dependencies     |
| Testing    | [e.g. Unity, CMocka]    | Test framework            |

## System Boundaries

- `src/` — source files (`.c`)
- `include/` — public headers (`.h`)
- `tests/` — test files
- `lib/` or `vendor/` — third-party libraries (if vendored)
- `build/` — build output (gitignored)
- `Makefile` or `CMakeLists.txt` — build definition

## Modules

- [module name] — [what it owns: e.g. memory pool, allocator]
- [module name] — [e.g. protocol parser, network I/O]
- [module name] — [e.g. data structures, storage]
- Each module: one `.h` (public API) + one `.c` (implementation)

## Memory Model

- [Allocation strategy — e.g. malloc/free per object, arena
  allocator, memory pool]
- [Ownership rules — e.g. caller allocates and frees, or
  module owns via create/destroy]
- [Lifetime documentation — which structs own which pointers]

## Error Model

- [Error strategy — e.g. return codes, errno, custom error enum]
- [Cleanup pattern — e.g. goto-cleanup, nested ifs, RAII-like macros]

## Build and Dependencies

- [How to build — e.g. `make`, `cmake --build build/`]
- [External dependencies and how they're linked]
- [Compiler flags enforced]

## Invariants

1. [e.g. Every malloc has a matching free in the same module]
2. [e.g. Public API functions validate all pointer parameters]
3. [e.g. No global mutable state — all state in structs passed explicitly]
4. [Rule]
