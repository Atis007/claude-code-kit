# Architecture Context

## Stack

| Layer      | Technology                          | Role                  |
| ---------- | ----------------------------------- | --------------------- |
| Language   | Python 3.13+                        | Backend language      |
| Framework  | [FastAPI / Flask / Django]           | HTTP framework        |
| Database   | [PostgreSQL / SQLite]               | Persistent storage    |
| ORM/Query  | [SQLAlchemy / Tortoise / raw asyncpg] | Data access         |
| Validation | [Pydantic]                          | Input/output schemas  |
| Auth       | [JWT / session]                     | Authentication        |
| Async      | [asyncio / sync]                    | Concurrency model     |
| Deploy     | [Docker / uvicorn]                  | Deployment            |

## System Boundaries

- `app/routes/` — route definitions, one file per resource
- `app/services/` — business logic
- `app/repositories/` — database access layer
- `app/models/` — domain models, dataclasses, Pydantic schemas
- `app/middleware/` — auth, error handling, logging
- `app/config.py` — configuration and env parsing
- `app/errors.py` — custom exception classes
- `tests/` — test files mirroring app structure

## Storage Model

- **Database**: [Tables and their responsibility]
- **File storage**: [What lives on disk or object storage]
- **Cache**: [What is cached — e.g. Redis, in-memory]

## Auth and Access Model

- [How authentication works]
- [How roles/permissions work]
- [How ownership is enforced]

## API Design

- RESTful resource endpoints
- Base path: [e.g. /api/v1/]
- [Pagination strategy]
- Pydantic models for all request/response shapes
- Auto-generated OpenAPI docs (if using FastAPI)

## Invariants

1. [e.g. Route handlers never access DB directly — through services]
2. [e.g. All external input validated with Pydantic before processing]
3. [e.g. Services receive and return typed models, not raw dicts]
4. [Rule]
