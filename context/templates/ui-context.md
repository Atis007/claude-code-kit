# UI Context

## Theme

[Describe the visual language — e.g. Dark-only technical
workspace, or light minimal with accent colors.]

## Dark Mode

[Strategy: class-based (`dark` class on html/body) or
media-based (`prefers-color-scheme`). If no dark mode,
state that explicitly.]

## Colors

All components must use these tokens — no hardcoded hex values.

| Role            | CSS Variable / Constant | Light     | Dark (if applicable) |
| --------------- | ----------------------- | --------- | -------------------- |
| Background      | `--bg-base`             | `#[hex]`  | `#[hex]`             |
| Surface         | `--bg-surface`          | `#[hex]`  | `#[hex]`             |
| Elevated        | `--bg-elevated`         | `#[hex]`  | `#[hex]`             |
| Primary text    | `--text-primary`        | `#[hex]`  | `#[hex]`             |
| Secondary text  | `--text-secondary`      | `#[hex]`  | `#[hex]`             |
| Muted text      | `--text-muted`          | `#[hex]`  | `#[hex]`             |
| Primary accent  | `--accent-primary`      | `#[hex]`  | `#[hex]`             |
| Accent hover    | `--accent-hover`        | `#[hex]`  | `#[hex]`             |
| Border          | `--border-default`      | `#[hex]`  | `#[hex]`             |
| Border subtle   | `--border-subtle`       | `#[hex]`  | `#[hex]`             |
| Error           | `--state-error`         | `#[hex]`  | `#[hex]`             |
| Warning         | `--state-warning`       | `#[hex]`  | `#[hex]`             |
| Success         | `--state-success`       | `#[hex]`  | `#[hex]`             |
| Info            | `--state-info`          | `#[hex]`  | `#[hex]`             |

For React Native: define these as a theme constants object
instead of CSS custom properties.

### Tailwind Theme Integration

If using Tailwind v4, define tokens in CSS with `@theme`:
```css
@theme {
  --color-bg-base: var(--bg-base);
  --color-bg-surface: var(--bg-surface);
  --color-accent: var(--accent-primary);
}
```

If using Tailwind v3, extend in `tailwind.config.ts`:
```ts
colors: {
  bg: { base: 'var(--bg-base)', surface: 'var(--bg-surface)' },
  accent: { primary: 'var(--accent-primary)' },
}
```

## Typography

| Role        | Font                          | Weight  | Size class  |
| ----------- | ----------------------------- | ------- | ----------- |
| Heading 1   | [e.g. Inter]                  | 700     | `text-3xl`  |
| Heading 2   | [e.g. Inter]                  | 600     | `text-2xl`  |
| Heading 3   | [e.g. Inter]                  | 600     | `text-xl`   |
| Body        | [e.g. Inter]                  | 400     | `text-base` |
| Body small  | [e.g. Inter]                  | 400     | `text-sm`   |
| Caption     | [e.g. Inter]                  | 400     | `text-xs`   |
| Code/mono   | [e.g. JetBrains Mono]         | 400     | `text-sm`   |

### Font Loading

[How fonts are loaded — e.g. Google Fonts, self-hosted,
system fonts. Specify the fallback stack.]

## Spacing Scale

[Define the spacing system — e.g. Tailwind defaults (4px base)
or custom scale.]

| Use             | Class     | Value  |
| --------------- | --------- | ------ |
| Tight (inline)  | `gap-1`   | 4px    |
| Default         | `gap-2`   | 8px    |
| Comfortable     | `gap-3`   | 12px   |
| Section         | `gap-4`   | 16px   |
| Card padding    | `p-4`     | 16px   |
| Page padding    | `p-6`     | 24px   |
| Section spacing | `py-8`    | 32px   |

## Border Radius

| Context           | Class          | Value |
| ----------------- | -------------- | ----- |
| Inline / small UI | `rounded-[?]`  | [?]   |
| Buttons / inputs  | `rounded-[?]`  | [?]   |
| Cards / panels    | `rounded-[?]`  | [?]   |
| Modals / overlays | `rounded-[?]`  | [?]   |
| Full round        | `rounded-full`  | 50%  |

## Shadows

| Context      | Class         |
| ------------ | ------------- |
| Cards        | `shadow-[?]`  |
| Dropdowns    | `shadow-[?]`  |
| Modals       | `shadow-[?]`  |
| None (flat)  | `shadow-none` |

## Transitions

Standard transition for interactive elements:
```
transition-colors duration-200 ease-in-out
```

[Specify duration and easing for the project — e.g.
150ms for micro-interactions, 300ms for layout changes.]

## Z-Index Scale

| Layer       | Value   | Usage                    |
| ----------- | ------- | ------------------------ |
| Base        | `z-0`   | Default content          |
| Dropdown    | `z-10`  | Dropdowns, tooltips      |
| Sticky      | `z-20`  | Sticky headers           |
| Overlay     | `z-30`  | Backdrop overlays        |
| Modal       | `z-40`  | Modal dialogs            |
| Toast       | `z-50`  | Notifications, toasts    |

## Component Library

[Base components — e.g. shadcn/ui, custom primitives,
React Native Paper. How to add new ones.]

### Component Conventions

- [How to handle disabled state — e.g. `opacity-50 pointer-events-none`]
- [Focus ring style — e.g. `focus-visible:ring-2 ring-accent-primary ring-offset-2`]
- [Loading state pattern — e.g. skeleton, spinner, disabled with spinner icon]

## Layout Patterns

- [Primary layout — e.g. Tab navigation with 4 tabs]
- [Screen structure — e.g. Header + scrollable content + fixed bottom action]
- [List patterns — e.g. Cards in vertical list with pull-to-refresh]
- [Modal patterns — e.g. Bottom sheet for forms, alert dialog for confirmations]
- [Empty states — e.g. centered icon + message + action button]
- [Max content width — e.g. `max-w-7xl mx-auto` for page content]

## Breakpoints

[Which breakpoints matter for this project, and what changes:]

| Breakpoint | Width  | Layout change                      |
| ---------- | ------ | ---------------------------------- |
| Base       | 0      | [e.g. Single column, stacked]      |
| `sm`       | 640px  | [e.g. ???]                         |
| `md`       | 768px  | [e.g. Sidebar appears]             |
| `lg`       | 1024px | [e.g. Three-column layout]         |
| `xl`       | 1280px | [e.g. Max-width content container] |

## Icons

[Icon library and sizes — e.g. Lucide React, 16px inline,
20px buttons, 24px navigation. Stroke width conventions.]

## Animations

[What animates and how — e.g. page transitions, list item
enter/exit, skeleton loading. Name the library if using one
(Framer Motion, GSAP). Keep it minimal unless the project
demands it.]

## Accessibility Baseline

- [Focus management — e.g. visible focus rings, skip links]
- [Color contrast target — e.g. WCAG AA minimum (4.5:1 text)]
- [Touch target size — e.g. minimum 44x44px on mobile]
- [Screen reader approach — e.g. semantic HTML first, aria when needed]
