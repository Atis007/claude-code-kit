# Architecture Context

## Stack

| Layer    | Technology                   | Role                   |
| -------- | ---------------------------- | ---------------------- |
| Language | PHP 8.4 (strict)             | Backend language       |
| Server   | [Apache / Nginx]             | HTTP server            |
| Database | [MySQL / PostgreSQL]         | Persistent storage     |
| Auth     | [JWT / Session]              | Authentication         |
| Deploy   | [e.g. shared hosting / Docker] | Deployment           |

No framework — custom routing, controllers, services.

## System Boundaries

- `public/` — entry point (index.php), front controller
- `src/Controllers/` — HTTP request handlers, one per resource
- `src/Services/` — business logic, one per domain concept
- `src/Repositories/` — database access layer
- `src/Middleware/` — auth, CORS, rate limiting
- `src/Routes/` — route definitions
- `src/Exceptions/` — custom exception classes
- `src/Utils/` — shared utilities
- `config/` — database, app config, env loading
- `migrations/` — versioned SQL files

## Storage Model

- **Database**: [Tables and their responsibility]
- **File system**: [What lives on disk, if anything]
- **Cache**: [What is cached and how, if applicable]

## Auth and Access Model

- [How authentication works]
- [How roles work]
- [How ownership is enforced]

## API Design

- RESTful resource endpoints
- [Versioning strategy — e.g. /api/v1/]
- [Pagination strategy — e.g. offset with page + per_page]

## Invariants

1. [e.g. Controllers never access DB directly — through repositories]
2. [e.g. Services never read $_GET/$_POST/$_SERVER — typed params only]
3. [e.g. All user input validated in controller before service call]
4. [Rule]
