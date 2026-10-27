---
name: saas-sidebar-redesign
description: Redesign a Vue 3 application's UI into a modern SaaS-style interface with a vertical left sidebar instead of a top nav bar, a consistent spacing scale, and a polished professional look. Use when asked to modernize, restyle, or restructure the layout or navigation of a Vue 3 app.
---

# SaaS Sidebar Redesign

Convert a Vue 3 app from a top navigation bar to a left vertical sidebar layout and apply a consistent design system across all views. Work in small verified steps; never change data logic, routes, or API calls.

## Ground Rules

- **Delegate `.vue` work to the `vue-expert` subagent** when this repo's CLAUDE.md requires it (creating or significantly modifying a `.vue` file).
- **Follow the project's existing conventions.** Read the root and `client/` CLAUDE.md first: component structure (Composition API, scoped styles), design system (colors, no emojis in UI), and unique `v-for` keys.
- **Visual change only.** Keep routes, props, emits, composables, i18n keys, and API calls intact. Reuse existing translation keys for labels; do not hardcode new strings if the app uses i18n.
- **Preserve existing behavior** of the header items being moved (language switcher, profile menu, filters, modals).

## Workflow

### 1. Audit
- Read `App.vue` (shell/layout), `main.js` (routes), and global styles.
- List: nav links and their routes, header widgets (profile, language, search), global bars (e.g. filters), and the modals mounted in the shell.
- Grep for hardcoded colors, px spacing, and font sizes to see how inconsistent the current styling is.
- Check whether any view assumes the top nav height (e.g. `position: sticky; top: Npx`, `calc(100vh - Npx)`).

### 2. Define design tokens
Put tokens in one place (`:root` in the global stylesheet, or `App.vue` if that is where global styles live). Reuse the project's palette where one exists.

```css
:root {
  /* Spacing: 4px base scale. Use only these values. */
  --space-1: 4px;  --space-2: 8px;   --space-3: 12px; --space-4: 16px;
  --space-5: 24px; --space-6: 32px;  --space-7: 48px;

  /* Layout */
  --sidebar-width: 240px;
  --sidebar-width-collapsed: 64px;
  --topbar-height: 56px;
  --content-max-width: 1280px;

  /* Surfaces and text (adapt to the project palette) */
  --color-bg: #f8fafc;
  --color-surface: #ffffff;
  --color-border: #e2e8f0;
  --color-text: #0f172a;
  --color-text-muted: #64748b;
  --color-accent: #2563eb;
  --color-sidebar-bg: #0f172a;
  --color-sidebar-text: #cbd5e1;
  --color-sidebar-active-bg: rgba(255, 255, 255, 0.08);

  /* Shape and depth */
  --radius-sm: 6px; --radius-md: 8px; --radius-lg: 12px;
  --shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.06);
  --shadow-md: 0 4px 12px rgba(15, 23, 42, 0.08);

  /* Type */
  --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  --text-xs: 12px; --text-sm: 14px; --text-md: 16px; --text-lg: 20px; --text-xl: 24px;
}
```

### 3. Build the shell (`App.vue`)
Target structure:

```
.app-shell (display: grid; grid-template-columns: var(--sidebar-width) 1fr)
  aside.sidebar        (fixed height 100vh, sticky, own scroll)
    .sidebar-brand     (company name + subtitle)
    nav.sidebar-nav    (router-links, one per route)
    .sidebar-footer    (profile menu, language switcher)
  .content-column
    header.topbar      (page title / filters, optional)
    main.main-content  (router-view, padded, max-width centered)
```

Requirements:
- Replace the top `<header>` / `.nav-tabs` with `<aside>` + `<nav aria-label="Primary">`. Keep every `router-link` and its label key.
- Active state: use `router-link-exact-active` / the existing `:class` binding; style with background, text color, and a 3px left accent bar. Do not rely on color alone.
- Move profile menu and language switcher to the sidebar footer (or topbar right side). Keep their events wired to the same handlers.
- Keep the global filter bar, now as a slim bar at the top of the content column.
- Icons: use inline SVG (stroke 1.5-2px, 20px, `currentColor`). No emojis, no icon-font dependency. Add `aria-hidden="true"` to decorative icons.
- Responsive: below ~900px, collapse the sidebar to an off-canvas drawer toggled by a menu button in the topbar (`aria-expanded`, `aria-controls`, close on route change and Escape). Optionally collapse to icon-only (`--sidebar-width-collapsed`) on medium widths.
- Keyboard and a11y: visible `:focus-visible` outline on every link/button, logical tab order, contrast of at least 4.5:1 for sidebar text.

### 4. Normalize the views
For each view and component, in this order: shell-adjacent components (FilterBar, modals), then views.
- Replace magic numbers with tokens: padding/margin/gap use `--space-*`; radii use `--radius-*`; font sizes use `--text-*`.
- One card pattern: `background: var(--color-surface); border: 1px solid var(--color-border); border-radius: var(--radius-lg); padding: var(--space-5); box-shadow: var(--shadow-sm)`.
- Consistent page header: title (`--text-xl`, weight 600), muted description, actions aligned right, `--space-5` below.
- Consistent grids: CSS Grid with `gap: var(--space-4)` or `--space-5`; no ad-hoc margins between siblings.
- Tables: sticky muted header row, `--space-3 --space-4` cell padding, subtle row hover, right-align numeric columns.
- Buttons: one primary, one secondary, one ghost style; consistent height (36px), hover, disabled, and focus states.
- Remove any `position: sticky; top: <old nav height>` or `calc(100vh - <old nav height>)` and fix to the new layout.
- Keep status colors semantic (green/blue/yellow/red) and consistent across badges, charts, and alerts.

### 5. Verify
- Run the dev server and confirm no console errors or warnings.
- Visit every route in a browser (use Playwright MCP tools if the project mandates them) at three widths: ~1440px, ~1024px, ~375px. Take screenshots.
- Check: active link highlights correctly on each route, sidebar does not scroll with page content, no horizontal overflow, modals still overlay correctly (z-index above sidebar), drawer opens and closes on mobile.
- Test keyboard navigation through the sidebar and the drawer.
- Run `npm run build` to confirm it compiles.
- Run existing tests if any are affected.

## Pitfalls
- Modals and dropdowns with a lower `z-index` than the sidebar will render underneath it.
- `100vh` on mobile browsers includes the address bar; prefer `100dvh` with a `100vh` fallback.
- Scoped styles do not reach child component roots; style via props/classes or deliberate `:deep()`.
- Do not duplicate global tokens inside scoped styles; reference the variables.
- Avoid a big-bang rewrite: land the shell first, confirm it works, then restyle views one at a time.

## Deliverable Summary
When done, report: files changed, tokens introduced, any views with intentionally unchanged styling and why, and screenshots or verification results for each viewport.
