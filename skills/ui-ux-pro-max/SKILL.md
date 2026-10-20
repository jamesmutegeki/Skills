---
name: ui-ux-pro-max
description: Use when building, reviewing, or redesigning UI components, screens, layouts, design systems, accessibility (a11y), responsive design, micro-interactions, design tokens, CSS/SCSS/Tailwind, component architecture, or any frontend-facing UX concern. Use ONLY when the task involves user interface or user experience design decisions — not for backend logic or infrastructure.
---

# UI/UX Pro Max

## Principles

- **Less is more**: every element must earn its place. Remove anything decorative that doesn't serve a function.
- **Design in the browser**: prefer working code over mockups. Use the model's knowledge of CSS, HTML, and frameworks to produce pixel-perfect output directly.
- **Accessibility is not optional**: all output must meet WCAG 2.2 AA at minimum. AAA where feasible.

## Design System & Tokens

When building a new UI, establish a consistent design system:

```css
/* Design tokens via CSS custom properties */
:root {
  /* Spacing scale (4px base) */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 24px;
  --space-6: 32px;
  --space-8: 48px;
  --space-10: 64px;

  /* Type scale */
  --text-xs: 0.75rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
  --text-2xl: 1.5rem;
  --text-3xl: 1.875rem;
  --text-4xl: 2.25rem;

  /* Border radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-xl: 16px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px -1px rgba(0,0,0,0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0,0,0,0.1);

  /* Transitions */
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  --duration-fast: 150ms;
  --duration-normal: 250ms;
  --duration-slow: 400ms;
}
```

### Theme-aware tokens

Always use `light-dark()` or prefers-color-scheme media queries:

```css
:root {
  --color-bg: light-dark(#ffffff, #0a0a0a);
  --color-fg: light-dark(#0a0a0a, #ededed);
  --color-muted: light-dark(#737373, #a3a3a3);
  --color-border: light-dark(#e5e5e5, #262626);
  --color-accent: light-dark(#0066cc, #3b82f6);
  --color-accent-fg: light-dark(#ffffff, #ffffff);
}
```

## Component Architecture

### Single Responsibility Components

Each component does one thing. Split when:

- A component takes more than ~5 props
- You find `if`/`else` branching on a prop to render completely different markup
- You need to pass `className` deep to a nested child

### Props API design

- Use `React.ComponentPropsWithoutRef<'button'>` extension for native HTML elements
- Forward refs via `forwardRef` for interactive elements
- Default to `data-*` attributes for styling hooks (avoid exposing internal DOM structure)

### Compound Components Pattern

```tsx
// Usage: <Menu> <Menu.Trigger /> <Menu.Items> <Menu.Item /> </Menu.Items> </Menu>
const Menu = ({ children }: { children: React.ReactNode }) => { ... }
Menu.Trigger = MenuTrigger
Menu.Items = MenuItems
Menu.Item = MenuItem
```

## Accessibility Checklist (WCAG 2.2 AA)

### Semantic HTML
- Use `<nav>`, `<main>`, `<aside>`, `<section>`, `<article>`, `<header>`, `<footer>` landmarks
- Heading hierarchy (h1 → h6) must be logical, never skip levels
- Use `<button>` for actions, `<a>` for navigation

### Focus Management
- All interactive elements must be keyboard-reachable (Tab)
- Focus indicators: always use `:focus-visible`, never `outline: none` alone
- Modal/drawer: trap focus, return on close
- Skip links at page top

### ARIA
- Use native HTML semantics first, ARIA as supplement
- `aria-label` on icon-only buttons
- `aria-expanded`, `aria-controls` for toggles
- `aria-live="polite"` for dynamic content updates
- `role="status"` for live regions

### Color & Contrast
- Text: minimum 4.5:1 contrast ratio (normal), 3:1 (large text)
- UI components / graphical objects: 3:1 minimum
- Never rely solely on color to convey information

### Motion
- Respect `prefers-reduced-motion`: replace animations with transitions
- All motion durations under 400ms to avoid vestibular trigger

## Responsive Design

### Breakpoints (mobile-first)
```css
/* Mobile: 0 - 639px (default) */
/* Tablet: 640px+ */
/* Desktop: 1024px+ */
/* Wide: 1280px+ */

@media (width >= 640px) { ... }
@media (width >= 1024px) { ... }
@media (width >= 1280px) { ... }
```

- Use `rem` / `em` for sizing, never `px` (except borders)
- Use `clamp()` for fluid typography: `font-size: clamp(1rem, 2.5vw, 1.5rem)`
- `container-type: inline-size` for container queries over viewport queries

## Micro-interactions

### Guidelines
- Duration: 150-300ms for UI feedback, 300-500ms for transitional elements
- Easing: `cubic-bezier(0.16, 1, 0.3, 1)` for natural feel
- Use `transform` and `opacity` only (GPU-composited, no layout thrashing)
- Stagger child animations by 30-50ms for polish

### Loading States
- Always show skeleton loaders matching final layout shape (not spinners)
- Optimistic UI for mutations; revert on error with toast

## Forms & Input

- Label every input (visible label or `aria-label`)
- Show validation errors inline, next to the field, not in a banner alone
- Use `type`, `inputMode`, `autocomplete`, `autoCapitalize` correctly
- Disable submit button only after first submission (to preserve form fill state)
- Preserve form state on validation failure

## Layout Patterns

- Use CSS Grid for page-level layout, Flexbox for component-level
- `grid-template-columns: repeat(auto-fill, minmax(280px, 1fr))` for responsive grids without media queries
- Keep content width maxed at ~720px (reading) or ~1200px (app UI), centered with horizontal padding
- Consistent whitespace: use the spacing scale, avoid arbitrary margins

## Performance-Aware UI

- `content-visibility: auto` for below-fold sections
- Lazy-load images with `loading="lazy"`
- Debounce search inputs (300ms)
- Virtualize long lists (use libraries like `react-virtuoso` or `@tanstack/virtual`)
- Code-split heavy components with `lazy()` / `React.lazy()`

## Error & Empty States

- Never show a blank screen. Every view needs: loading, empty, error, and success states.
- Error messages must be human-readable, actionable, and specific
- Empty states should guide the user (e.g., "No results. Try adjusting filters." with a CTA)

## File Organization

```
src/
  components/
    ui/          # Primitive: Button, Input, Dialog, Select
    layout/      # Shell: Header, Sidebar, Main
    screens/     # Page-level: DashboardPage, SettingsPage
    shared/      # Domain-specific: UserCard, ActivityFeed
  hooks/
  styles/
  utils/
  types/
```
