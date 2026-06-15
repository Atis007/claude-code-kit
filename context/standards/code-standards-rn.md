# Code Standards — React Native (Expo)

React Native + Expo conventions. Use with `code-standards-typescript.md`.

## Version

Target Expo SDK 52+ and React Native 0.76+ (New Architecture
enabled by default). Use current features:

- New Architecture (Fabric renderer, TurboModules) — enabled
  by default in SDK 52+, no opt-in needed
- `expo-router` v4+ for file-based routing (if used)
- `expo-image` over `Image` for better caching and performance
- `expo-sqlite` with synchronous API (SDK 52+)
- React 19 features available in latest Expo

## Components

- Functional components only
- One component per file, file name matches component name
- Props interface named `{Component}Props`
- Co-locate component-specific types, helpers, and constants —
  extract only when shared
- `ref` as a normal prop (React 19) — no `forwardRef`

## Component Structure

Order within a component:
1. Props destructuring
2. Hooks (state, Redux selectors, refs, effects)
3. Derived values and handlers
4. Early returns (loading, error, empty states)
5. Return JSX

## State Management

- Local state (`useState`) for UI-only state (modals, input
  values, animation triggers)
- Redux for application state that persists across screens or
  needs to survive navigation (user data, cached entities,
  app settings)
- Keep Redux slices focused — one slice per domain concept
- Selectors for reading Redux state — do not access store
  shape directly in components
- Dispatch actions from event handlers, not from effects
  when avoidable

## Local Database (expo-sqlite)

- All database access through a dedicated data layer —
  components never run SQL directly
- Typed query results — define interfaces for each table row
- Use the synchronous API (`useSQLiteContext`) for simpler code
  in SDK 52+
- Migrations: versioned, forward-only, run on app start
- Use transactions for multi-statement writes
- `db.getAllSync()` / `db.runSync()` for synchronous operations
  when appropriate

## Navigation

- Expo Router v4+ for file-based routing (preferred for new
  projects) or React Navigation — document in `architecture.md`
- Type route params — no untyped `params.id` access
- Use typed routes with Expo Router:
  ```tsx
  router.push({ pathname: '/user/[id]', params: { id: userId } });
  ```
- Keep screen components thin — delegate logic to hooks or
  services, screen is a layout shell

## Styling

- `StyleSheet.create` for static styles — defined at the
  bottom of the component file
- Dynamic styles via style arrays:
  ```tsx
  <View style={[styles.card, isActive && styles.cardActive]} />
  ```
- No inline style objects for static values — they create
  new objects on every render
- Project design tokens (in `ui-context.md`) via a theme
  module — no hardcoded hex values
- Platform-specific styles via `Platform.select` when needed
- If using NativeWind (Tailwind for RN), follow the Tailwind
  conventions from `code-standards-react.md` adapted for RN:
  - `className` prop for styling
  - Same utility class rules apply
  - Use NativeWind's `vars()` for CSS variable equivalents

## Images

- Use `expo-image` instead of React Native's `Image`:
  - Built-in caching and placeholder support
  - Better performance with Fabric
  - `contentFit` instead of `resizeMode`
- Specify dimensions — don't rely on intrinsic image sizing
- Use appropriate image formats (WebP when possible)

## Performance

- `FlatList` for any list that can exceed ~20 items — never
  `ScrollView` with `.map()` for dynamic lists
- `FlashList` from `@shopify/flash-list` as a drop-in
  replacement for better performance on large lists
- `keyExtractor` with stable, unique keys — not array index
- `getItemLayout` when items have fixed height
- Minimize re-renders in lists: keep item components pure,
  avoid passing new objects/functions as props every render
- Do not memoize by default — memoize when measured
- Use `useTransition` for non-urgent updates (React 19)

## Hooks

- Custom hooks for reusable stateful logic
- Go in `hooks/` or co-located with the feature
- No `useEffect` for derived state — compute inline
- `useEffect` cleanup: always clean up subscriptions,
  listeners, and timers

## File Organization

- `app/` — route screens (Expo Router)
- `screens/` — screen components (React Navigation)
- `components/` — shared components
- `components/ui/` — base UI primitives
- `features/` — feature-specific components by domain
- `hooks/` — shared custom hooks
- `store/` — Redux store, slices, selectors
- `db/` — database schema, migrations, data access layer
- `lib/` or `utils/` — pure utility functions
- `types/` — shared type definitions
- `constants/` — colors, config values, enums
- `assets/` — images, fonts, static resources

## Platform Considerations

- Test on both iOS and Android unless the project targets one
- Use `Platform.OS` checks sparingly — prefer cross-platform
  components
- Safe area: `SafeAreaView` or `useSafeAreaInsets` for
  edge-to-edge screens
- Keyboard: `KeyboardAvoidingView` with platform-specific
  behavior prop
- Haptics: `expo-haptics` for tactile feedback on interactions

## Error Handling

- Global error boundary at app root for crash recovery
- Per-screen or per-feature error boundaries for isolated failures
- User-facing error messages: clear, actionable, no stack traces
- Network errors: distinguish offline from server errors
