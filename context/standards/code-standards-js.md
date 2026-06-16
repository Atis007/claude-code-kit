# Code Standards — JavaScript

Vanilla JavaScript conventions. For projects not using
TypeScript — Node.js backends, scripts, browser-side code,
or mixed codebases.

## Version

Target ES2025+. Use current features:

### ES2025

- **Set methods** — native set operations, no libraries:
  ```js
  const a = new Set([1, 2, 3]);
  const b = new Set([2, 3, 4]);
  a.union(b)              // Set {1, 2, 3, 4}
  a.intersection(b)       // Set {2, 3}
  a.difference(b)         // Set {1}
  a.symmetricDifference(b) // Set {1, 4}
  a.isSubsetOf(b)         // false
  a.isDisjointFrom(b)     // false
  ```
- **Iterator helpers** — chainable lazy operations on any iterator:
  ```js
  const results = items.values()
    .filter(item => item.active)
    .map(item => item.name)
    .take(10)
    .toArray();
  ```
- **`Promise.try()`** — wraps sync or async function into a promise:
  ```js
  const result = await Promise.try(() => maybeSync());
  ```
- **Import attributes** — type-safe module imports:
  ```js
  import data from './config.json' with { type: 'json' };
  import styles from './app.css' with { type: 'css' };
  ```

### ES2022–2024

- **`Promise.withResolvers()`** (ES2024) — cleaner deferred promise pattern:
  ```js
  const { promise, resolve, reject } = Promise.withResolvers();
  ```
- **`Object.groupBy()` / `Map.groupBy()`** (ES2024) — native grouping:
  ```js
  const byStatus = Object.groupBy(orders, o => o.status);
  // { pending: [...], shipped: [...] }
  ```
- **Non-mutating array methods** (ES2023) — return new arrays:
  ```js
  arr.toSorted((a, b) => a - b)  // sorted copy
  arr.toReversed()                // reversed copy
  arr.toSpliced(1, 2)             // spliced copy
  arr.with(0, 'new')              // replaced copy at index
  ```
- **`Array.prototype.findLast()` / `findLastIndex()`** —
  search from the end
- **`structuredClone()`** — deep clone without JSON tricks:
  ```js
  const copy = structuredClone(original);
  ```
- **`Object.hasOwn(obj, prop)`** (ES2022) — replaces
  `Object.prototype.hasOwnProperty.call()`
- **`Array.prototype.at()`** (ES2022) — negative indexing:
  `arr.at(-1)` for last element

## Modules

- ES modules (`import`/`export`) for all new code
- `"type": "module"` in `package.json` for Node.js projects
- Named exports preferred over default exports — explicit
  and refactor-friendly
- One module per concern — no giant `utils.js` catch-all
- No CommonJS (`require`) in new code unless the ecosystem
  forces it

## Naming

- `camelCase` for variables, functions, parameters
- `PascalCase` for classes and constructor functions
- `UPPER_SNAKE_CASE` for true constants (config, enum-like objects)
- Boolean variables: `is`, `has`, `should`, `can` prefix
- Event handlers: `handle` prefix (`handleClick`, `handleSubmit`)
- File names: `kebab-case.js` or `camelCase.js` — pick one,
  be consistent project-wide

## Functions

- Arrow functions for callbacks and short expressions
- Named function declarations for top-level functions
  (better stack traces)
- One function, one job
- Early returns over deep nesting
- Default parameters over manual checks:
  ```js
  // yes
  function createUser(name, role = 'user') { ... }

  // no
  function createUser(name, role) {
    role = role || 'user';
  }
  ```
- Rest parameters (`...args`) over `arguments` object
- Destructuring in parameters for objects:
  ```js
  function createOrder({ userId, items, currency = 'EUR' }) { ... }
  ```

## Variables

- `const` by default — `let` only when reassignment is needed
- Never `var`
- Declare close to first use, not at the top of the function
- Destructure objects and arrays when accessing multiple properties:
  ```js
  const { name, email, role } = user;
  const [first, ...rest] = items;
  ```

## Objects and Arrays

- Shorthand property names: `{ name, email }` not `{ name: name }`
- Computed property names when needed: `{ [key]: value }`
- Spread for shallow copies: `{ ...obj, updated: true }`
- Optional chaining for nested access: `user?.address?.city`
- Nullish coalescing for defaults: `value ?? fallback`
  (not `||` which catches `0`, `''`, `false`)
- Use non-mutating array methods (`toSorted`, `toReversed`,
  `toSpliced`, `with`) when you need a new array — don't
  clone-then-mutate

## Async

- `async`/`await` for all asynchronous operations
- No raw `.then()` chains unless composing dynamic promise pipelines
- `Promise.all()` for concurrent independent operations
- `Promise.allSettled()` when you need results of all promises
  regardless of failure
- `Promise.withResolvers()` when you need to resolve/reject
  from outside the executor
- Top-level `await` in modules
- Always handle rejections — no unhandled promise rejections

## Error Handling

- `try/catch` at recovery points, not everywhere
- Custom error classes for domain errors:
  ```js
  class NotFoundError extends Error {
    constructor(entity, id) {
      super(`${entity} not found: ${id}`);
      this.name = 'NotFoundError';
      this.entity = entity;
      this.id = id;
    }
  }
  ```
- `Error.cause` for chaining:
  ```js
  throw new AppError('user load failed', { cause: originalError });
  ```
- Never swallow errors silently (`catch (e) {}`)
- Use `finally` for cleanup

## Validation

- Validate all external input at system boundaries (API input,
  URL params, file reads, user input)
- Use a validation library (Zod, Joi, Yup) at boundaries —
  define in `architecture.md`
- After validation, trust the data downstream
- JSDoc type annotations for function signatures when not
  using TypeScript (see JSDoc section)

## JSDoc

Use JSDoc for type documentation when not using TypeScript:
```js
/**
 * @param {string} id
 * @param {{ includeOrders?: boolean }} [options]
 * @returns {Promise<User>}
 */
async function getUser(id, options = {}) { ... }

/** @typedef {{ id: string, name: string, email: string }} User */

/** @type {Map<string, User>} */
const cache = new Map();
```

Enable `// @ts-check` at the top of files for editor-level
type checking with JSDoc annotations.

## Classes

- Use classes when you need multiple instances with shared behavior
- Private fields with `#`:
  ```js
  class UserService {
    #db;
    constructor(db) { this.#db = db; }
  }
  ```
- Static methods for factory patterns and utilities
- Prefer composition over inheritance — use classes for
  encapsulation, not deep hierarchies

## Iterators

Use iterator helpers for lazy processing:
```js
// yes — lazy, processes only what's needed
const firstTen = items.values()
  .filter(item => item.active)
  .map(item => transform(item))
  .take(10)
  .toArray();

// no — materializes entire array at each step
const firstTen = items
  .filter(item => item.active)
  .map(item => transform(item))
  .slice(0, 10);
```

Use `Object.groupBy` over manual reduce:
```js
// yes
const byCategory = Object.groupBy(products, p => p.category);

// no
const byCategory = products.reduce((acc, p) => {
  (acc[p.category] ??= []).push(p);
  return acc;
}, {});
```

## Testing

- Test framework in `architecture.md` (Vitest, Jest, node:test)
- `node:test` for simple Node.js projects (stdlib, no deps)
- Test names: `test('functionName — scenario — expected result')`
- Mock at boundaries (I/O, external services), not internals
- `describe` blocks to group related tests

## File Organization

- `src/` — source code
- `tests/` or `__tests__/` — test files
- `lib/` or `utils/` — shared pure utilities
- `config/` — configuration loading
- Entry point: `src/index.js` or `src/main.js`
- One module per concern

## Node.js Specifics (if applicable)

- `"type": "module"` in `package.json`
- `node:` prefix for built-in modules: `import fs from 'node:fs/promises'`
- `node:test` for simple test suites
- `--env-file .env` flag (Node 20.6+) over dotenv
- Built-in `fetch` (stable since Node 21, no node-fetch)
- `import.meta.dirname` / `import.meta.filename` over
  `__dirname` / `__filename`
