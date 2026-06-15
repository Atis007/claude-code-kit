# Architecture Context

## Stack

| Layer      | Technology                       | Role                    |
| ---------- | -------------------------------- | ----------------------- |
| Language   | Rust (2024 edition)              | Backend / systems       |
| Framework  | [axum / actix-web / none]        | HTTP framework          |
| Runtime    | [tokio]                          | Async runtime           |
| Database   | [PostgreSQL / SQLite]            | Persistent storage      |
| DB Driver  | [sqlx / diesel / sea-orm]        | Database access         |
| Serialization | [serde + serde_json]          | Data serialization      |
| Auth       | [JWT / session]                  | Authentication          |
| Deploy     | [Docker / static binary]         | Deployment              |

## System Boundaries

- `src/main.rs` — entry point, server startup, config loading
- `src/config.rs` — configuration and env parsing
- `src/error.rs` — error types and conversions
- `src/routes/` — HTTP route handlers
- `src/services/` — business logic
- `src/repositories/` — database access layer
- `src/models/` — domain types, request/response structs
- `src/middleware/` — tower middleware (auth, logging)

## Storage Model

- **Database**: [Tables and their responsibility]
- **File storage**: [What lives on disk, if anything]
- **Cache**: [What is cached, if applicable]

## Auth and Access Model

- [How authentication works]
- [How roles/permissions work]
- [How ownership is enforced]

## Error Strategy

- [Error crate — e.g. thiserror for domain errors,
  anyhow for application-level]
- [How errors convert to HTTP responses — e.g. IntoResponse impl]
- [Logging strategy — e.g. tracing crate with structured spans]

## API Design

- RESTful resource endpoints
- Base path: [e.g. /api/v1/]
- [Pagination strategy]
- Serde models for all request/response shapes
- Tower middleware for cross-cutting concerns

## Invariants

1. [e.g. Route handlers never access DB directly — through services]
2. [e.g. All unsafe blocks have a SAFETY comment]
3. [e.g. No unwrap() outside of tests]
4. [Rule]
