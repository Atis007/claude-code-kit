# Code Standards — Express.js

Express.js + TypeScript backend conventions. Use with
`code-standards-typescript.md`.

## Version

Target Express 5+ and Node.js 22+ (LTS). Key changes from Express 4:

- Rejected promises in handlers automatically call `next(err)` —
  no need for manual try-catch wrappers or `express-async-errors`
- Path route matching uses updated syntax (path-to-regexp 8 — named
  wildcards like `/*splat`, no bare regex strings by default)
- `req.query` is now a getter (not reassignable); brackets-based
  query parsing is the default
- Removed deprecated methods (`req.acceptsCharset`, `app.del`, etc.)

Node.js 22+ runtime features to use:
- Built-in `fetch` (stable since Node 21, no node-fetch needed)
- `node:test` runner for simple tests
- `--env-file` flag (Node 20.6+) for loading `.env` without dotenv
- `import.meta.dirname` and `import.meta.filename` (ESM)
- Stable WebSocket client (`WebSocket` global)

## Architecture Pattern

- **Routes** define endpoints and wire middleware. No logic.
- **Controllers** handle HTTP: parse request, call service,
  format response. No business logic.
- **Services** contain business logic. Typed input/output.
  No `req`/`res` awareness.
- **Repositories** handle database queries. Services call
  repositories, not the DB client directly.
- One router per resource, one service per domain concept.

## Request/Response

- Validate input at controller or middleware level with a
  schema validator (Zod preferred for TS inference) — define
  in `architecture.md`
- Consistent JSON response shapes:
  ```ts
  // Success
  { data: { ... } }
  { data: [...], meta: { total: 42, page: 1 } }

  // Error
  { error: { code: 'VALIDATION_ERROR', message: '...' } }
  ```
- Appropriate HTTP status codes
- Type request handlers with explicit param, body, response types

## Middleware

- Auth middleware verifies identity and attaches user to `req`
- Validation middleware parses request body/params
- Error handling middleware is last — catches all thrown errors
- Order: CORS → body parser → auth → route validation →
  handler → error handler
- No business logic in middleware
- Express 5 handles async errors automatically — no wrapper needed:
  ```ts
  // Express 5 — this just works, rejected promises go to error handler
  router.get('/:id', async (req, res) => {
    const user = await userService.getById(req.params.id);
    res.json({ data: user });
  });
  ```

## Error Handling

- Throw typed errors from services, catch in error middleware
- Custom error class with status code and error code:
  ```ts
  class AppError extends Error {
    constructor(
      public statusCode: number,
      public code: string,
      message: string
    ) { super(message); }
  }
  ```
- No stack traces to client in production
- Log errors with context (request ID, user ID, endpoint)

## Database

- DB client choice in `architecture.md` (Prisma, Drizzle, Knex, raw pg)
- Typed query results — no `any` from DB
- Parameterized queries — no string concatenation
- Transactions for multi-statement writes
- Migrations: versioned, forward-only

## Naming

- `camelCase` for variables, functions, handlers
- `PascalCase` for types, interfaces, classes
- Route paths: lowercase, kebab-case, plural nouns
  (`/api/users`, `/api/order-items`)
- Controllers: `getAll`, `getById`, `create`, `update`, `remove`
- Services: verb-noun (`createUser`, `validateOrder`)

## File Organization

- `src/routes/` — route definitions per resource
- `src/controllers/` — request handlers
- `src/services/` — business logic
- `src/repositories/` — database access
- `src/middleware/` — auth, validation, error handling
- `src/errors/` — custom error classes
- `src/types/` — shared type definitions
- `src/utils/` — pure utilities
- `src/config/` — env parsing, configuration
- `src/index.ts` — entry point

## Environment and Config

- Use `--env-file .env` flag (Node 20.6+) or dotenv — pick one
- Parse and validate env vars at startup with Zod — fail fast
  if missing:
  ```ts
  const envSchema = z.object({
    PORT: z.coerce.number().default(3000),
    DATABASE_URL: z.string().url(),
    JWT_SECRET: z.string().min(32),
  });
  export const env = envSchema.parse(process.env);
  ```
- No `.env` in git — use `.env.example`

## Security

- Helmet for security headers
- CORS: explicit origins in production
- Rate limiting on auth and public endpoints
- Input validation on every endpoint accepting data
- Auth check before any mutation
