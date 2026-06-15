# Architecture Context

## Stack

| Layer      | Technology                      | Role                   |
| ---------- | ------------------------------- | ---------------------- |
| Runtime    | Node.js + TypeScript            | Server runtime         |
| Framework  | Express.js                      | HTTP framework         |
| Database   | [PostgreSQL / MySQL / SQLite]   | Persistent storage     |
| ORM/Query  | [Prisma / Knex / raw pg]        | Database access        |
| Validation | [Zod / Joi]                     | Input validation       |
| Auth       | [JWT / session]                 | Authentication         |
| Deploy     | [e.g. Docker / Railway]         | Deployment             |

## System Boundaries

- `src/routes/` — route definitions, one file per resource
- `src/controllers/` — HTTP request handlers
- `src/services/` — business logic
- `src/repositories/` — database access layer
- `src/middleware/` — auth, validation, error handling, CORS
- `src/errors/` — custom error classes
- `src/types/` — shared type definitions
- `src/utils/` — pure utilities
- `src/config/` — env parsing, app config
- `src/index.ts` — entry point, server startup

## Storage Model

- **Database**: [Tables and their responsibility]
- **File storage**: [What lives on disk or object storage]
- **Cache**: [What is cached, if applicable]

## Auth and Access Model

- [How authentication works]
- [How roles/permissions work]
- [How ownership is enforced]

## API Design

- RESTful resource endpoints
- Base path: `/api/v1/`
- [Pagination strategy]
- [Rate limiting strategy]

## Invariants

1. [e.g. Controllers never import DB client — through repositories]
2. [e.g. Services have no knowledge of req/res objects]
3. [e.g. All input validated before reaching controller body]
4. [Rule]
