# Architecture Context

## Stack

| Layer      | Technology                  | Role                    |
| ---------- | --------------------------- | ----------------------- |
| Language   | Go 1.23+                   | Backend / systems       |
| Router     | [net/http / chi / gin]      | HTTP routing            |
| Database   | [PostgreSQL / MySQL / SQLite] | Persistent storage    |
| DB Driver  | [database/sql / sqlx / pgx] | Database access        |
| Auth       | [JWT / session]             | Authentication          |
| Deploy     | [Docker / binary]           | Deployment              |

## System Boundaries

- `cmd/` — entry points, one subdirectory per binary
- `internal/` — private application code
- `internal/handler/` — HTTP handlers (request/response)
- `internal/service/` — business logic
- `internal/repository/` — database access
- `internal/middleware/` — HTTP middleware (auth, logging)
- `internal/model/` — domain types and structs
- `internal/config/` — configuration loading
- `migrations/` — SQL migration files

## Storage Model

- **Database**: [Tables and their responsibility]
- **File system**: [What lives on disk, if anything]
- **Cache**: [What is cached, if applicable]

## Auth and Access Model

- [How authentication works]
- [How roles/permissions work]
- [How ownership is enforced]

## API Design

- RESTful resource endpoints
- [Versioning strategy]
- [Pagination strategy]
- Graceful shutdown with signal handling

## Invariants

1. [e.g. Handlers never access DB directly — through repositories]
2. [e.g. Services accept and return domain types, not HTTP types]
3. [e.g. Every goroutine has a cancellation path via context]
4. [Rule]
