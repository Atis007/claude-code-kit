# Architecture Context

## Stack

| Layer      | Technology                         | Role                     |
| ---------- | ---------------------------------- | ------------------------ |
| Framework  | React Native + Expo (managed)      | Mobile app framework     |
| Language   | TypeScript (strict)                | Type-safe development    |
| State      | Redux Toolkit                      | Global state management  |
| Local DB   | expo-sqlite                        | On-device storage        |
| Navigation | [Expo Router / React Navigation]   | Screen routing           |
| Styling    | [StyleSheet / NativeWind]          | Component styling        |
| Charts     | [e.g. React Native Gifted Charts]  | Data visualization       |

## System Boundaries

- `app/` or `screens/` — route/screen components
- `components/` — shared, reusable components
- `components/ui/` — base UI primitives
- `features/` — feature-specific components by domain
- `hooks/` — shared custom hooks
- `store/` — Redux store, slices, selectors
- `db/` — database schema, migrations, data access layer
- `lib/` or `utils/` — pure utility functions
- `types/` — shared type definitions
- `constants/` — colors, config values, enums

## Storage Model

- **Redux store**: [What lives in Redux — e.g. working set
  of transactions, UI state, selected category]
- **SQLite**: [What persists on device — e.g. all transactions,
  categories, currencies, user preferences]
- **Relationship**: [How they sync — e.g. SQLite is source of
  truth, Redux holds the working set loaded on app start]

## Auth and Access Model

- [e.g. Local-only app with no auth, or token-based with backend]
- [Single user or multi-user on device]

## Data Flow

- [e.g. Screen dispatches action → thunk calls DB layer →
  DB runs SQL → result stored in Redux → screen reads selector]

## Invariants

1. [e.g. Components never run SQL — all DB access through db/]
2. [e.g. Redux is runtime cache, SQLite is source of truth]
3. [e.g. All screens handle loading, error, and empty states]
4. [Rule]
