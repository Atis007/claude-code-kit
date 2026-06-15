# Code Standards — PHP

PHP 8.4+ REST API conventions. No framework — custom routing,
controllers, and services.

## Version

Target PHP 8.4+. Use current language features:

- **Property hooks** (8.4) — custom get/set logic without
  boilerplate getters/setters:
  ```php
  class User {
      public string $name {
          set => trim($value);
      }
      public string $displayName {
          get => "{$this->name} ({$this->role->value})";
      }
  }
  ```
- **Asymmetric visibility** (8.4) — different access for
  read vs write:
  ```php
  class Transaction {
      public private(set) readonly string $id;
      public private(set) float $amount;
  }
  ```
- **`new` without parentheses** (8.4) — `new UserDto` instead
  of `new UserDto()`
- **`array_find()`**, **`array_any()`**, **`array_all()`** (8.4) —
  use instead of manual loops for searching/checking arrays
- **`#[\Override]`** (8.3) — mark methods that override a parent
- **`#[\Deprecated]`** (8.4) — mark deprecated code with messages
- **Enums** (8.1) — for all fixed value sets
- **Readonly classes** (8.2) — for immutable DTOs
- **Typed class constants** (8.3)
- **Match expressions** — instead of switch for value returns
- **Named arguments** — for clarity in calls with many parameters
- **Fibers** — only when async is genuinely needed

## General

- `declare(strict_types=1)` in every file
- PSR-12 coding style
- PSR-4 autoloading via Composer

## Architecture Pattern

- **Controllers** handle HTTP concerns: parse request, validate
  input, call service, return response. No business logic.
- **Services** contain business logic. Receive typed data, return
  results. No HTTP awareness — should work from CLI too.
- **Repositories** handle database access. Raw SQL or thin query
  builder — no ORM unless specified.
- One controller per resource, one service per domain concept.

## Request/Response

- Validate and parse all request input at the controller level
- Consistent JSON response shapes:
  ```php
  // Success
  { "data": { ... } }
  { "data": [ ... ], "meta": { "total": 42, "page": 1 } }

  // Error
  { "error": { "code": "VALIDATION_ERROR", "message": "..." } }
  ```
- Appropriate HTTP status codes — don't return 200 for errors
- `Content-Type: application/json` on all API responses

## DTOs and Value Objects

Use readonly classes with asymmetric visibility for DTOs:
```php
readonly class CreateUserDto {
    public function __construct(
        public string $name,
        public string $email,
        public Role $role = Role::User,
    ) {}
}
```

Use property hooks for computed or validated properties:
```php
class Money {
    public int $cents {
        get => (int)($this->amount * 100);
    }

    public function __construct(
        public private(set) float $amount,
        public private(set) string $currency,
    ) {}
}
```

## Routing

- Centralized route definitions — one file or class mapping
  method + path to controller + action
- RESTful: GET /items, GET /items/{id}, POST /items,
  PUT /items/{id}, DELETE /items/{id}
- Route parameters validated before reaching the controller

## Database

- Prepared statements for every query — no string concatenation
  of user input into SQL
- Transactions for multi-statement writes
- Typed return values from repository methods
- Migrations: versioned SQL files, tracked in a migrations table

## Auth and Access

- Auth check in middleware or controller start — before logic
- Ownership checks in the service layer
- Never trust client-side identity claims

## Error Handling

- Exceptions for exceptional conditions, not control flow
- Custom exception classes: `NotFoundException`,
  `ValidationException`, `UnauthorizedException`
- Global exception handler → HTTP response conversion
- Never expose stack traces or SQL to the client

## Enums

Use backed enums for all fixed value sets:
```php
enum Role: string {
    case Admin = 'admin';
    case User = 'user';
    case Moderator = 'moderator';

    public function canDelete(): bool {
        return match($this) {
            self::Admin, self::Moderator => true,
            default => false,
        };
    }
}
```

## Types and Structure

- Type hints on all parameters and return types
- Union types and intersection types where appropriate
- `readonly` on properties that shouldn't change after construction
- No magic arrays passed between layers — define the shape
  with classes or readonly classes

## Naming

- `PascalCase` for classes
- `camelCase` for methods and variables
- `UPPER_SNAKE_CASE` for constants
- Controller methods: `index`, `show`, `store`, `update`, `destroy`
- Service methods: verb-noun (`createUser`, `validateTicket`)

## File Organization

- `src/Controllers/` — HTTP controllers
- `src/Services/` — business logic
- `src/Repositories/` — database access
- `src/Dto/` — data transfer objects
- `src/Enum/` — backed enums
- `src/Middleware/` — request middleware (auth, CORS)
- `src/Exceptions/` — custom exception classes
- `src/Routes/` — route definitions
- `src/Utils/` — shared utilities
- `config/` — configuration files
- `migrations/` — SQL migration files
- `public/` — entry point (index.php)

## Security

- CORS: explicit allowed origins, not `*` in production
- Rate limiting on auth endpoints
- Input sanitization for rendered data
- `password_hash()` with `PASSWORD_ARGON2ID` (preferred) or
  `PASSWORD_BCRYPT`
- Secrets in environment variables, not in code
