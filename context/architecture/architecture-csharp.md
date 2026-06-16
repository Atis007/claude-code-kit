# Architecture Context

## Stack

| Layer       | Technology                        | Role                   |
| ----------- | --------------------------------- | ---------------------- |
| Language    | C# 14 / .NET 10+ (LTS)           | Backend language       |
| Framework   | [ASP.NET Core / console / worker] | Application type       |
| Database    | [PostgreSQL / SQL Server / SQLite] | Persistent storage    |
| ORM         | [EF Core / Dapper]               | Data access            |
| Auth        | [ASP.NET Identity / JWT]         | Authentication         |
| Validation  | [FluentValidation / DataAnnotations] | Input validation   |
| Deploy      | [Docker / Azure / IIS]           | Deployment             |

## System Boundaries

- `Controllers/` — HTTP endpoints, one per resource
- `Services/` — business logic, one per domain concept
- `Repositories/` — data access layer (or EF Core DbContext directly)
- `Models/` — domain entities
- `Dtos/` — request/response data transfer objects
- `Middleware/` — custom middleware (error handling, logging)
- `Extensions/` — service registration extension methods
- `Configuration/` — typed options classes

## Storage Model

- **Database**: [Tables and their responsibility]
- **File storage**: [What lives on disk or blob storage]
- **Cache**: [What is cached — e.g. IMemoryCache, Redis]

## Auth and Access Model

- [How authentication works]
- [How roles/policies work]
- [How ownership is enforced]

## Dependency Injection

- All services registered in `Program.cs` or via extension methods
- [Lifetime strategy — e.g. services scoped, repositories scoped,
  config singletons]

## API Design

- RESTful resource endpoints
- Base path: [e.g. /api/v1/]
- [Pagination strategy]
- [Rate limiting — e.g. built-in rate limiter middleware]

## Invariants

1. [e.g. Controllers never access DbContext directly — through services]
2. [e.g. Entities never returned from API — map to DTOs]
3. [e.g. All dependencies via constructor injection, no service locator]
4. [Rule]
