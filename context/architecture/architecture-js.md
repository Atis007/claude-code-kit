# Architecture Context

## Stack

| Layer      | Technology                          | Role                   |
| ---------- | ----------------------------------- | ---------------------- |
| Language   | JavaScript (ES2025+)                | Application language   |
| Runtime    | [Node.js 22+ / Browser / Deno / Bun] | Execution environment |
| Framework  | [Express / Fastify / Hono / none]   | HTTP framework         |
| Database   | [PostgreSQL / SQLite / MongoDB]     | Persistent storage     |
| DB Driver  | [pg / better-sqlite3 / Prisma]      | Database access        |
| Validation | [Zod / Joi / Yup]                   | Input validation       |
| Auth       | [JWT / session]                     | Authentication         |
| Build      | [none / esbuild / Vite]             | Bundling (if needed)   |
| Deploy     | [Docker / direct binary / serverless] | Deployment           |

## Module System

- ES modules (`import`/`export`) with `"type": "module"` in
  `package.json`
- [Or specify CommonJS if legacy project]

## System Boundaries

- `src/routes/` — route definitions, one file per resource
- `src/controllers/` — request handlers
- `src/services/` — business logic
- `src/repositories/` — database access layer
- `src/middleware/` — auth, validation, error handling
- `src/errors/` — custom error classes
- `src/utils/` — pure utility functions
- `src/config/` — configuration and env parsing
- `src/index.js` — entry point

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
- Base path: [e.g. /api/v1/]
- [Pagination strategy]
- [Rate limiting strategy]

## Invariants

1. [e.g. Controllers never access DB directly — through services]
2. [e.g. All external input validated at the boundary before processing]
3. [e.g. Services have no knowledge of req/res objects]
4. [Rule]
