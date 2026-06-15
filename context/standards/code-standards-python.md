# Code Standards — Python

Python backend / scripting conventions.

## Version

Target Python 3.13+. Use current features:

- **Type parameter syntax** (3.12+) — generic functions and
  classes without `TypeVar` boilerplate:
  ```python
  def first[T](items: list[T]) -> T | None:
      return items[0] if items else None

  class Stack[T]:
      def __init__(self) -> None:
          self._items: list[T] = []
  ```
- **`type` keyword** (3.12+) — explicit type aliases:
  ```python
  type UserId = int
  type Result[T] = T | Error
  type Matrix = list[list[float]]
  ```
- **`@override` decorator** (3.12+) from `typing` — mark methods
  that override a parent, caught at type-check time
- **Improved f-strings** (3.12+) — nested quotes, backslashes,
  comments inside f-strings all work now
- **`match` statements** (3.10+) — structural pattern matching:
  ```python
  match event:
      case OrderCreated(id=order_id):
          handle_new(order_id)
      case OrderPaid(amount=amount) if amount > 0:
          handle_payment(amount)
      case _:
          log.warning(f"Unknown event: {event}")
  ```
- **Exception groups** (3.11+) — `ExceptionGroup`, `except*`
  for concurrent error handling
- **`TaskGroup`** (3.11+) — structured concurrency for asyncio
- **`tomllib`** (3.11+) — TOML parsing in stdlib
- **`dataclasses`** — for structured data (prefer over plain dicts)
- **`StrEnum`** (3.11+) — string enums with auto-lowercase

## General

- Type hints on all function signatures — parameters and return types
- `ruff` for linting and formatting (replaces flake8, isort, black)
- `mypy` or `pyright` in strict mode for type checking
- `pyproject.toml` for project configuration — no `setup.py`
- Virtual environments: `uv` (preferred), `poetry`, or `venv`

## Type Hints

- All function signatures typed — no exceptions
- Use built-in generics: `list[int]`, `dict[str, User]`,
  `tuple[int, ...]` — not `typing.List`, `typing.Dict`
- `X | None` instead of `Optional[X]`
- `X | Y` instead of `Union[X, Y]`
- Type aliases with `type` keyword for complex types
- `TypedDict` for dictionary shapes that cross boundaries:
  ```python
  class UserResponse(TypedDict):
      id: str
      name: str
      email: str
  ```
- Use `Protocol` for structural typing (duck typing with
  type safety):
  ```python
  class Renderable(Protocol):
      def render(self) -> str: ...
  ```

## Naming

- `snake_case` for functions, variables, methods, modules, packages
- `PascalCase` for classes, type aliases, protocols, exceptions
- `UPPER_SNAKE_CASE` for module-level constants
- `_leading_underscore` for private/internal names
- Boolean variables/functions: `is_`, `has_`, `can_`, `should_` prefix
- No abbreviations except universally understood ones

## Data Structures

- `dataclass` for structured data with behavior:
  ```python
  @dataclass(frozen=True, slots=True)
  class User:
      id: str
      name: str
      email: str
      role: Role = Role.USER
  ```
- `frozen=True` for immutable data, `slots=True` for performance
- `NamedTuple` for lightweight immutable records
- `Enum` / `StrEnum` for fixed value sets:
  ```python
  class Status(StrEnum):
      ACTIVE = auto()
      INACTIVE = auto()
      SUSPENDED = auto()
  ```
- Avoid bare dicts for structured data — use dataclasses or TypedDict

## Functions

- One function, one job
- Early returns over deep nesting
- Default arguments: immutable only (`None`, not `[]` or `{}`)
- `*args` and `**kwargs` sparingly — explicit params are clearer
- Use keyword-only arguments (`*,`) for clarity:
  ```python
  def create_user(*, name: str, email: str, role: Role = Role.USER) -> User:
  ```
- Generators for lazy sequences — don't materialize large lists

## Error Handling

- Specific exception types — never bare `except:` or `except Exception:`
  without re-raising
- Custom exceptions per domain error:
  ```python
  class NotFoundError(Exception):
      def __init__(self, entity: str, id: str) -> None:
          super().__init__(f"{entity} not found: {id}")
  ```
- `raise ... from err` to chain exceptions with context
- Never swallow exceptions silently
- Context managers (`with`) for resource cleanup

## Async

- `asyncio` for I/O-bound concurrent work
- `TaskGroup` (3.11+) for structured concurrency:
  ```python
  async with asyncio.TaskGroup() as tg:
      task1 = tg.create_task(fetch_user(uid))
      task2 = tg.create_task(fetch_orders(uid))
  user, orders = task1.result(), task2.result()
  ```
- `async`/`await` for all I/O — no mixing sync and async
- Don't use threads for I/O — use async

## Testing

- `pytest` as the test framework
- Test names: `test_function_name_scenario_expected_result`
- `@pytest.mark.parametrize` for table-driven tests
- Fixtures for setup/teardown
- Mock at boundaries (I/O, external services) — not internals
- `pytest-asyncio` for async tests

## Dependencies

- Pin versions in `pyproject.toml` or `requirements.txt`
- `uv` for fast dependency resolution (preferred)
- Minimize dependencies — stdlib is extensive
- Separate prod and dev dependencies

## File Organization

- Package structure with `__init__.py`:
  ```
  src/
  ├── app/
  │   ├── __init__.py
  │   ├── routes/
  │   ├── services/
  │   ├── repositories/
  │   ├── models/
  │   └── config.py
  └── tests/
  ```
- One module per concern — not one giant `utils.py`
- `__all__` in `__init__.py` for explicit public API

## API (if building an HTTP service)

- Framework in `architecture.md` (FastAPI, Flask, Django)
- FastAPI preferred for new projects — async, typed, auto-docs
- Pydantic models for request/response validation
- Consistent JSON response shapes
- Proper HTTP status codes
- Dependency injection via framework features
