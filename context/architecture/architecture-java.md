# Architecture Context

## Stack

| Layer      | Technology                         | Role                   |
| ---------- | ---------------------------------- | ---------------------- |
| Language   | Java 17+ (LTS)                     | Backend language       |
| Framework  | [Spring Boot / Quarkus / plain]    | Application framework  |
| Build      | [Maven / Gradle]                   | Build and dependency   |
| Database   | [PostgreSQL / MySQL]               | Persistent storage     |
| ORM        | [JPA/Hibernate / JDBI / jOOQ]      | Data access            |
| Auth       | [Spring Security / JWT / session]  | Authentication         |
| Deploy     | [Docker / JAR]                     | Deployment             |

## System Boundaries

- `controller/` — HTTP request handlers, one per resource
- `service/` — business logic, one per domain concept
- `repository/` — data access layer
- `dto/` — request/response data transfer objects
- `model/` or `entity/` — domain entities / DB entities
- `config/` — application configuration classes
- `exception/` — custom exception classes and global handler
- `middleware/` or `filter/` — cross-cutting concerns

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

1. [e.g. Controllers never access repositories directly — through services]
2. [e.g. Entities never leak into API responses — use DTOs]
3. [e.g. All dependencies injected via constructor, no field injection]
4. [Rule]
