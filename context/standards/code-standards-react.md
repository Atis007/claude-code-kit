# Code Standards — React

React web frontend conventions. Use with `code-standards-typescript.md`.

## React Version

Target React 19+. Use current features:

- `use()` hook for reading resources (promises, context) in render
- `useOptimistic` for optimistic UI updates
- `useFormStatus` for form submission state
- `ref` as a regular prop — no `forwardRef` wrapper needed
- `<form action={fn}>` for form actions when applicable
- `useTransition` for non-urgent state updates
- Automatic batching of state updates (default since React 18)

## Components

- Functional components only — no class components
- One component per file, file name matches component name
  (`UserCard.tsx` exports `UserCard`)
- Co-locate component-specific types, helpers, and constants
  in the same file — extract only when shared
- Props interface named `{Component}Props`:
  ```tsx
  interface UserCardProps {
    user: User;
    onSelect: (id: string) => void;
  }
  ```
- `ref` is a normal prop now — no `forwardRef`:
  ```tsx
  function Input({ ref, ...props }: InputProps & { ref?: React.Ref<HTMLInputElement> }) {
    return <input ref={ref} {...props} />;
  }
  ```

## Component Structure

Order within a component:
1. Props destructuring
2. Hooks (state, context, refs, effects)
3. Derived values and handlers
4. Early returns (loading, error, empty states)
5. Return JSX

## State

- Local state (`useState`) for UI-only state (open/closed,
  input values, hover)
- Lift state up only when a sibling needs it — not preemptively
- Context for cross-cutting concerns (theme, auth, locale) —
  not for all shared state
- `use(Context)` can replace `useContext` in React 19
- External state management (Redux, Zustand) for server-synced
  or complex shared state — define when to use in `architecture.md`

## Hooks

- Custom hooks for reusable stateful logic — not for extracting
  a function that could be plain
- Custom hooks start with `use`, go in `hooks/` or co-located
  with the feature
- `useEffect` must have a clear purpose comment if the
  dependency array is non-obvious
- No `useEffect` for derived state — compute inline:
  ```tsx
  // yes
  const fullName = `${first} ${last}`;

  // no
  const [fullName, setFullName] = useState('');
  useEffect(() => setFullName(`${first} ${last}`), [first, last]);
  ```

## Performance

- Do not memoize by default — the React Compiler (v1.0, opt-in
  build-time tool, not automatic React 19 runtime behavior) reduces
  the need for manual memoization
- `React.memo` only when you've measured a real performance problem
- `useMemo` for genuinely expensive computations (filtering
  large lists, complex transforms) — not simple derivations
- `useCallback` only when passing callbacks to memoized children
  or dependency arrays
- `useTransition` to keep the UI responsive during heavy
  state updates

## Tailwind CSS

### Core Rules

- Tailwind utility classes for all styling — no inline style
  objects unless dynamic values require it (e.g. `style={{ width: calculatedWidth }}`)
- No `@apply` in stylesheets except for third-party component
  overrides — it defeats the purpose of utility-first. If you're
  reaching for `@apply`, make a component instead.
- No hardcoded hex/rgb values in classes — use your design tokens
  from `ui-context.md` via CSS custom properties or Tailwind theme

### Class Organization

Order classes consistently within an element. Group by concern:

1. Layout (`flex`, `grid`, `block`, `absolute`, `relative`)
2. Sizing (`w-`, `h-`, `min-w-`, `max-h-`)
3. Spacing (`p-`, `m-`, `gap-`)
4. Typography (`text-`, `font-`, `leading-`, `tracking-`)
5. Visual (`bg-`, `border-`, `rounded-`, `shadow-`)
6. Interactive (`hover:`, `focus:`, `active:`, `transition-`)

No need to be religious about order — consistency within the
project is what matters.

### Conditional Classes

Use `clsx` or `cn` (from shadcn) for conditional classes:
```tsx
<div className={cn(
  'rounded-lg border p-4 transition-colors',
  isActive ? 'border-blue-500 bg-blue-50' : 'border-gray-200',
  isDisabled && 'opacity-50 pointer-events-none'
)} />
```

### Responsive Design

- Mobile-first: base classes for mobile, `sm:`, `md:`, `lg:`,
  `xl:` for larger screens
- Breakpoints define minimum widths — `md:flex` means "flex
  at medium and above"
- Don't mix mobile-first and desktop-first in the same project
- Container queries (`@container`, `@lg:`) for component-level
  responsiveness when the component's container size matters
  more than the viewport

### Dark Mode

- Use `dark:` variant if the project supports dark mode
- Define the strategy in `ui-context.md` (class-based or
  media-based)
- Always pair light and dark values for custom colors

### Tailwind v4 Specifics

If using Tailwind v4+:

- CSS-first configuration — use `@theme` in your CSS instead
  of `tailwind.config.js`:
  ```css
  @theme {
    --color-primary: #3b82f6;
    --color-surface: #1e1e1e;
    --radius-card: 0.75rem;
  }
  ```
- Automatic content detection — no `content` config needed
- All theme values are CSS variables by default — access with
  `var(--color-primary)` in JS when needed
- `@utility` directive for custom utilities
- `@variant` directive for custom variants

If using Tailwind v3:

- `tailwind.config.ts` for theme extension
- CSS custom properties defined in `:root` and referenced in config

### What to Avoid

- No `!important` via `!` prefix — if you need it, the
  specificity problem should be fixed at the source
- No arbitrary values (`text-[13px]`, `bg-[#ff0000]`) unless
  the value genuinely doesn't fit any scale — if it happens
  often, extend the theme
- Don't extract Tailwind classes into string constants:
  ```tsx
  // no — this loses editor tooling and is hard to scan
  const cardClasses = 'rounded-lg border p-4 shadow-sm';

  // yes — make a component instead
  function Card({ children }: { children: React.ReactNode }) {
    return <div className="rounded-lg border p-4 shadow-sm">{children}</div>;
  }
  ```
- Don't fight Tailwind with custom CSS for things Tailwind
  already handles

### Tailwind Merge

Use `tailwind-merge` (usually via `cn` helper) when composing
classes from props to handle conflicts:
```tsx
import { twMerge } from 'tailwind-merge';
import { clsx } from 'clsx';

function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Last class wins for conflicting utilities
cn('p-4', 'p-6') // → 'p-6'
cn('text-red-500', props.className) // → caller can override
```

### Animations and Transitions

- Use Tailwind's built-in transition utilities (`transition-colors`,
  `transition-opacity`, `duration-200`, `ease-in-out`)
- For complex animations, use `animate-` classes or define
  keyframes in CSS / Tailwind config
- Keep transitions short: 150-300ms for UI interactions

## File Organization

- `components/` — shared/reusable components
- `components/ui/` — base UI primitives (button, input, modal)
- `features/` or page-level folders — feature-specific components
- `hooks/` — shared custom hooks
- `lib/` or `utils/` — pure utility functions, `cn` helper
- `types/` — shared type definitions

## Forms

- Controlled inputs with explicit state
- `useFormStatus` for submit button state in React 19
- `useOptimistic` for immediate UI feedback on form submission
- Validate on submit, show errors after first submit attempt
- Disable submit during async operations
- Clear error state when the user edits the relevant field

## API Communication

- All API calls through a single client/wrapper — not raw
  `fetch` scattered across components
- Handle loading, error, and success states explicitly
- Type API responses at the boundary
