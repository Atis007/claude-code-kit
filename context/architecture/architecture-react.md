# Architecture Context

## Stack

| Layer     | Technology                           | Role                      |
| --------- | ------------------------------------ | ------------------------- |
| Framework | React + TypeScript                   | SPA frontend              |
| Styling   | Tailwind CSS                         | Utility-first styling     |
| UI Lib    | [e.g. shadcn/ui]                     | Base component primitives |
| State     | [e.g. Redux / Zustand / Context]     | Client state management   |
| Routing   | [e.g. React Router / TanStack Router]| Client-side routing       |
| API       | [e.g. REST / GraphQL]                | Backend communication     |
| Build     | [e.g. Vite]                          | Dev server and bundler    |

## System Boundaries

- `components/` — shared, reusable UI components
- `components/ui/` — base primitives (button, input, modal)
- `features/` — feature-specific components, hooks, logic by domain
- `hooks/` — shared custom hooks
- `lib/` — API client, utilities, shared logic
- `types/` — shared TypeScript type definitions
- `pages/` or `routes/` — top-level route components

## Storage Model

- **Client state**: [What lives in React state / state manager]
- **Server state**: [What is fetched from backend on demand]
- **Local storage**: [What persists in browser, if anything]

## Auth and Access Model

- [How authentication works]
- [How the frontend knows the user's role/permissions]
- [How protected routes work]

## Invariants

1. [e.g. Components never call the API directly — all through lib/]
2. [e.g. No business logic in components — extract to hooks or utils]
3. [Rule]
4. [Rule]
