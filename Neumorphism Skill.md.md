---
name: neumorphism-ui-kit
description: Design, build, review, and theme accessible neumorphic (Soft UI) interfaces. Provides design tokens, component recipes (buttons, cards, inputs, navigation, modals, tables, charts, alerts, empty states), page patterns (dashboards, forms, auth, mobile), dark mode, motion, accessibility rules, framework adapters, theme generation, and a code-review rubric. Use this skill whenever the user mentions neumorphism, neumorphic, soft UI, soft shadows, raised or pressed "extruded" surfaces, or asks for a UI with dual light/dark shadows, even if they do not use those exact words. Also use it when auditing or fixing the accessibility, contrast, or consistency of an existing soft-UI codebase.
version: 1.1.0
---

# Neumorphism UI Kit

Neumorphism (Soft UI) makes elements look extruded from, or pressed into, a single shared surface using a light shadow and a dark shadow. It is attractive and also fragile: low contrast and shadow-only affordances fail users unless the design is reinforced with real borders, text, icons, and focus rings. **This skill's job is to deliver the soft aesthetic without sacrificing usability.**

---

## 1. Operating procedure

Follow this order for every task.

1. **Inspect the project first.** Identify the framework, styling approach (plain CSS, CSS Modules, Sass, Tailwind, styled-components, Emotion, MUI, Chakra), existing theme files, existing components, routing, and available dependencies.
2. **Reuse before creating.** Extend existing tokens and components. Never build a second design system alongside an existing one.
3. **Select the sections that apply** using the routing table below.
4. **Plan the smallest safe change.** Do not rewrite unrelated files. Preserve existing behavior.
5. **Implement** with shared tokens, semantic HTML, and visible focus states.
6. **Verify.** Run the formatter, linter, type check, and tests when available. Walk the quality checklist (section 14). Check keyboard use, 200% zoom, a 360px viewport, and dark mode if present.
7. **Report.** Summarize the files changed, key decisions, and remaining limitations (especially contrast trade-offs).

If the project has no code yet, state your assumptions (framework, styling) in one line and proceed rather than asking a long list of questions.

### Routing table

| Task | Sections to apply |
|---|---|
| New design system / theme | 2, 3, 4, 9 |
| Button, card, input, badge, alert | 2, 3, 5 |
| Navigation, modal, table, chart, empty state | 3, 6 |
| Form, login/signup, OTP | 3, 5.3, 7.2, 7.3, 9 |
| Dashboard / admin panel | 3, 5.2, 6.4, 7.1, 7.4 |
| Mobile / responsive work | 7.4, 9 |
| Dark mode | 4, 9 |
| Animation / transitions | 8 |
| Accessibility audit or fix | 9, 14 |
| Existing code review | 11, 9, 14 |
| Generate a theme from a brand color | 10 |
| New project scaffold | 13 |
| Framework-specific code | 12 |

---

## 2. Design principles

1. **Shared surface.** Components use `--neu-surface`, which equals or sits very close to the page `--neu-bg`. Do not give each component its own background.
2. **Dual shadow.** Raised = light shadow top-left + dark shadow bottom-right. Pressed/inset = the same pair, inverted with `inset`.
3. **Hierarchy through elevation.** Large containers `lg`, cards `md`, controls `sm`, inputs and active items `inset`, dense content areas flat. Never raise every nested element.
4. **Shadows are decoration, never information.** State, status, focus, and boundaries must also be communicated through text, icons, borders, outlines, or `aria-*` attributes.
5. **Tokens only.** No ad-hoc shadow, color, radius, or spacing values inside components.
6. **Restraint.** Few elevation levels, sparing accent color, short transitions, no gratuitous blur.

### Non-negotiables

- Real `<button>`, `<a>`, `<nav>`, `<form>`, `<table>`: no clickable `<div>`s.
- Visible `:focus-visible` outline on every interactive element (never shadow-only).
- Text contrast >= 4.5:1 (3:1 for large text). Control boundaries and focus indicators >= 3:1 (WCAG 1.4.11).
- Visible labels; placeholder is never the only label.
- Status never conveyed by color or shadow alone.
- `prefers-reduced-motion` respected.
- Touch targets >= 44x44px.
- Works with shadows disabled (test this).

---

## 3. Foundation tokens

Create or extend one global token file (for example `styles/tokens.css`). The values below are tuned for contrast on `#e0e5ec`; verify with a contrast checker if you change the surface.

```css
:root {
  /* Surfaces */
  --neu-bg: #e0e5ec;
  --neu-surface: #e0e5ec;

  /* Text (>= 4.5:1 on --neu-surface) */
  --neu-text: #273142;
  --neu-text-secondary: #4a5568;
  --neu-text-muted: #5b667a;

  /* Accent */
  --neu-accent: #635bff;        /* fills, rings, large text */
  --neu-accent-hover: #5148e5;
  --neu-accent-text: #4338ca;   /* accent-colored body text on surface */
  --neu-accent-soft: rgba(99, 91, 255, 0.16);
  --neu-on-accent: #ffffff;

  /* Semantic (text-safe on --neu-surface) */
  --neu-success: #0f6e43;
  --neu-warning: #8a5300;
  --neu-danger: #b71c1c;
  --neu-info: #1769aa;

  /* Boundaries and focus */
  --neu-border: rgba(89, 101, 121, 0.28);   /* decorative dividers */
  --neu-border-strong: #6c778a;             /* control outlines (>= 3:1) */
  --neu-focus: #4338ca;

  /* Shadow colors */
  --neu-shadow-light: rgba(255, 255, 255, 0.8);
  --neu-shadow-dark: rgba(163, 177, 198, 0.6);
  --neu-accent-shadow: rgba(65, 58, 160, 0.35);

  /* Elevation */
  --neu-shadow-xs: 2px 2px 5px var(--neu-shadow-dark), -2px -2px 5px var(--neu-shadow-light);
  --neu-shadow-sm: 4px 4px 8px var(--neu-shadow-dark), -4px -4px 8px var(--neu-shadow-light);
  --neu-shadow-md: 8px 8px 16px var(--neu-shadow-dark), -8px -8px 16px var(--neu-shadow-light);
  --neu-shadow-lg: 14px 14px 28px var(--neu-shadow-dark), -14px -14px 28px var(--neu-shadow-light);
  --neu-shadow-inset: inset 6px 6px 12px var(--neu-shadow-dark), inset -6px -6px 12px var(--neu-shadow-light);

  /* Radius */
  --neu-radius-xs: 6px;
  --neu-radius-sm: 10px;
  --neu-radius-md: 16px;
  --neu-radius-lg: 24px;
  --neu-radius-xl: 32px;
  --neu-radius-pill: 999px;

  /* Spacing (4px scale) */
  --neu-space-1: 4px;   --neu-space-2: 8px;   --neu-space-3: 12px;
  --neu-space-4: 16px;  --neu-space-5: 20px;  --neu-space-6: 24px;
  --neu-space-7: 32px;  --neu-space-8: 40px;  --neu-space-9: 48px;
  --neu-space-10: 64px;

  /* Typography */
  --neu-font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  --neu-font-size-xs: 0.75rem;  --neu-font-size-sm: 0.875rem; --neu-font-size-md: 1rem;
  --neu-font-size-lg: 1.125rem; --neu-font-size-xl: 1.5rem;   --neu-font-size-2xl: 2rem;
  --neu-font-size-3xl: 3rem;
  --neu-line-height-tight: 1.2; --neu-line-height-normal: 1.5; --neu-line-height-relaxed: 1.7;

  /* Motion */
  --neu-duration-fast: 120ms;
  --neu-duration-normal: 180ms;
  --neu-duration-slow: 280ms;
  --neu-ease-standard: cubic-bezier(0.2, 0.8, 0.2, 1);

  /* Layout */
  --neu-content-width: 1200px;
  --neu-sidebar-width: 260px;
  --neu-header-height: 72px;
}
```

### Base reset and utilities

```css
*, *::before, *::after { box-sizing: border-box; }
html { color-scheme: light; }
body {
  margin: 0;
  background: var(--neu-bg);
  color: var(--neu-text);
  font-family: var(--neu-font-family);
  line-height: var(--neu-line-height-normal);
  text-rendering: optimizeLegibility;
}
button, input, textarea, select { font: inherit; }
button { border: 0; }
img, svg { display: block; max-width: 100%; }

:focus-visible { outline: 3px solid var(--neu-focus); outline-offset: 3px; }

.sr-only {
  position: absolute; width: 1px; height: 1px; padding: 0; margin: -1px;
  overflow: hidden; clip: rect(0, 0, 0, 0); white-space: nowrap; border: 0;
}

@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    scroll-behavior: auto !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Foundation completion criteria

All colors, shadows, radii, spacing, and type are tokens; no component contains a raw shadow value; focus, disabled, and reduced-motion are defined; light and dark themes can coexist.

---

## 4. Dark mode

Dark neumorphism needs a *darker* shadow and a *much subtler* highlight, otherwise the light shadow glows.

```css
[data-theme="dark"] {
  color-scheme: dark;

  --neu-bg: #20242b;
  --neu-surface: #20242b;

  --neu-text: #f4f7fb;
  --neu-text-secondary: #c4ccd8;
  --neu-text-muted: #9ba7b7;

  --neu-accent: #8b83ff;
  --neu-accent-hover: #a39dff;
  --neu-accent-text: #aaa5ff;
  --neu-accent-soft: rgba(139, 131, 255, 0.2);
  --neu-on-accent: #14122e;

  --neu-success: #5dd39e;
  --neu-warning: #ffc266;
  --neu-danger: #ff7676;
  --neu-info: #70b7ff;

  --neu-border: rgba(255, 255, 255, 0.1);
  --neu-border-strong: #7f8aa0;
  --neu-focus: #aaa5ff;

  --neu-shadow-light: rgba(255, 255, 255, 0.035);
  --neu-shadow-dark: rgba(0, 0, 0, 0.58);
  --neu-accent-shadow: rgba(0, 0, 0, 0.5);
  /* Re-declare the shadow tokens so they pick up the new colors */
  --neu-shadow-xs: 2px 2px 5px var(--neu-shadow-dark), -2px -2px 5px var(--neu-shadow-light);
  --neu-shadow-sm: 4px 4px 8px var(--neu-shadow-dark), -4px -4px 8px var(--neu-shadow-light);
  --neu-shadow-md: 8px 8px 16px var(--neu-shadow-dark), -8px -8px 16px var(--neu-shadow-light);
  --neu-shadow-lg: 14px 14px 28px var(--neu-shadow-dark), -14px -14px 28px var(--neu-shadow-light);
  --neu-shadow-inset: inset 6px 6px 12px var(--neu-shadow-dark), inset -6px -6px 12px var(--neu-shadow-light);
}

/* To follow the system preference without JS, declare the same dark values once more
   inside @media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) { ... } },
   or generate both blocks from a single source (Sass mixin, Style Dictionary, etc.). */
```

Because CSS custom properties resolve where they are *declared*, shadow tokens that reference `--neu-shadow-dark` must be re-declared inside the dark block (as above) or defined with `:root, [data-theme]` as the selector.

### Theme switching rules

- Respect the system preference by default; persist an explicit user choice.
- Prevent a flash of the wrong theme with a tiny inline script in `<head>`:

```html
<script>
  try {
    var t = localStorage.getItem("theme");
    if (!t) t = matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light";
    document.documentElement.dataset.theme = t;
  } catch (e) {}
</script>
```

- The toggle is a `<button>` with a label that reflects the *action* ("Switch to dark mode") and `aria-pressed` or an updated label after toggling.
- Re-verify focus rings, muted text, disabled states, and charts in both themes. Do not blindly invert images or illustrations.

---

## 5. Component recipes

All recipes assume the tokens above. Class prefix: `neu-`.

### 5.1 Buttons

**Variants:** primary, secondary, tertiary, ghost, destructive, success, icon-only, floating action, link-style.
**States to implement:** default, hover, active/pressed, focus-visible, disabled, loading, success, error.

```html
<button type="button" class="neu-button neu-button--primary">Create project</button>
<button type="button" class="neu-button neu-button--secondary">Cancel</button>
<button type="button" class="neu-button neu-button--icon" aria-label="Open settings">⚙</button>

<!-- Loading: keep the button in the layout, block re-submission -->
<button type="submit" class="neu-button neu-button--primary" aria-disabled="true" aria-busy="true">
  <span class="neu-spinner" aria-hidden="true"></span><span>Saving…</span>
</button>
```

```css
.neu-button {
  display: inline-flex; min-height: 44px; align-items: center; justify-content: center; gap: 8px;
  border: 2px solid var(--neu-border-strong);
  border-radius: var(--neu-radius-md);
  padding: 12px 20px;
  background: var(--neu-surface); color: var(--neu-text);
  box-shadow: var(--neu-shadow-sm);
  cursor: pointer; font-weight: 700; line-height: 1;
  transition: transform var(--neu-duration-normal) var(--neu-ease-standard),
              box-shadow var(--neu-duration-normal) var(--neu-ease-standard),
              background-color var(--neu-duration-normal) var(--neu-ease-standard);
}
.neu-button:hover:not(:disabled):not([aria-disabled="true"]) { transform: translateY(-1px); box-shadow: var(--neu-shadow-md); }
.neu-button:active:not(:disabled):not([aria-disabled="true"]),
.neu-button[aria-pressed="true"] { transform: translateY(1px); box-shadow: var(--neu-shadow-inset); }
.neu-button:disabled, .neu-button[aria-disabled="true"] { opacity: 0.6; cursor: not-allowed; box-shadow: none; }

.neu-button--primary {
  border-color: transparent; background: var(--neu-accent); color: var(--neu-on-accent);
  box-shadow: 6px 6px 12px var(--neu-accent-shadow), -4px -4px 10px var(--neu-shadow-light);
}
.neu-button--primary:hover:not(:disabled) { background: var(--neu-accent-hover); }
.neu-button--danger { border-color: transparent; background: var(--neu-danger); color: #fff; }
.neu-button--ghost { border-color: transparent; background: transparent; box-shadow: none; }
.neu-button--icon { width: 44px; padding: 0; border-radius: 50%; }

.neu-spinner {
  width: 1em; height: 1em; border: 2px solid currentColor; border-right-color: transparent;
  border-radius: 50%; animation: neu-spin 0.8s linear infinite;
}
@keyframes neu-spin { to { transform: rotate(360deg); } }
```

**Rules:** `type="button"` unless submitting; `aria-label` on icon-only buttons; use `aria-disabled` plus a click guard (rather than `disabled`) when a loading button must stay focusable; never hide critical text on small screens without an accessible alternative; the pressed state is a *bonus*, so toggles also need `aria-pressed`.

### 5.2 Cards

**Types:** informational, metric, profile, product, interactive, feature, warning/success, media, empty-state.

```html
<article class="neu-card">
  <header class="neu-card__header">
    <div>
      <p class="neu-card__eyebrow">Weekly progress</p>
      <h2 class="neu-card__title">Study performance</h2>
    </div>
    <span class="neu-badge neu-badge--success"><span aria-hidden="true">▲</span> +12%</span>
  </header>
  <div class="neu-card__body">
    <p class="neu-card__value">78%</p>
    <p class="neu-card__description">Completion rate compared with last week.</p>
  </div>
  <footer class="neu-card__footer">
    <button type="button" class="neu-button neu-button--secondary">View details</button>
  </footer>
</article>
```

```css
.neu-card { border-radius: var(--neu-radius-lg); padding: var(--neu-space-6); background: var(--neu-surface); box-shadow: var(--neu-shadow-md); }
.neu-card--flat { box-shadow: none; border: 1px solid var(--neu-border); }
.neu-card--inset { box-shadow: var(--neu-shadow-inset); }
.neu-card__header, .neu-card__footer { display: flex; align-items: center; justify-content: space-between; gap: var(--neu-space-4); }
.neu-card__title, .neu-card__description, .neu-card__value { margin: 0; }
.neu-card__eyebrow { margin: 0 0 var(--neu-space-2); color: var(--neu-text-muted); font-size: var(--neu-font-size-sm); font-weight: 700; }
.neu-card__title { font-size: var(--neu-font-size-lg); }
.neu-card__body { margin: var(--neu-space-6) 0; }
.neu-card__value { font-size: var(--neu-font-size-3xl); font-weight: 800; line-height: var(--neu-line-height-tight); }
.neu-card__description { margin-top: var(--neu-space-2); color: var(--neu-text-secondary); }

.neu-badge {
  display: inline-flex; align-items: center; gap: 4px; border-radius: var(--neu-radius-pill);
  padding: 4px 10px; font-size: var(--neu-font-size-sm); font-weight: 700;
  background: var(--neu-surface); box-shadow: var(--neu-shadow-xs); border: 1px solid var(--neu-border-strong);
}
.neu-badge--success { color: var(--neu-success); }
.neu-badge--warning { color: var(--neu-warning); }
.neu-badge--danger  { color: var(--neu-danger); }
.neu-badge--info    { color: var(--neu-info); }
```

**Interactive cards:** use a real link or button (stretch it with `::after` if the whole card is clickable), add `:focus-visible`, and never make the whole card clickable if it contains other unrelated actions.

### 5.3 Inputs

**Controls:** text, search, select, textarea, checkbox, radio, switch.
**Always provide:** visible label, focus, error, disabled, required, and helper states; keyboard support; 44px+ target.

```html
<div class="neu-field">
  <label for="email" class="neu-field__label">Email address <span aria-hidden="true">*</span></label>
  <input id="email" name="email" type="email" class="neu-input" autocomplete="email"
         aria-describedby="email-help email-error" aria-invalid="true" required />
  <p id="email-help" class="neu-field__help">Use the email associated with your account.</p>
  <p id="email-error" class="neu-field__error"><span aria-hidden="true">⚠</span> Enter an email like name@example.com.</p>
</div>
```

```css
.neu-field { display: grid; gap: var(--neu-space-2); }
.neu-field__label { color: var(--neu-text); font-weight: 700; }
.neu-field__help { margin: 0; color: var(--neu-text-secondary); font-size: var(--neu-font-size-sm); }
.neu-field__error { margin: 0; color: var(--neu-danger); font-size: var(--neu-font-size-sm); font-weight: 600; }

.neu-input, .neu-select, .neu-textarea {
  width: 100%; border: 2px solid var(--neu-border-strong); border-radius: var(--neu-radius-md);
  padding: 12px 14px; background: var(--neu-surface); color: var(--neu-text);
  box-shadow: var(--neu-shadow-inset);
}
.neu-input { min-height: 46px; }
.neu-textarea { min-height: 120px; resize: vertical; }
.neu-input::placeholder, .neu-textarea::placeholder { color: var(--neu-text-muted); }
.neu-input:focus-visible, .neu-select:focus-visible, .neu-textarea:focus-visible {
  outline: 3px solid var(--neu-focus); outline-offset: 2px; border-color: var(--neu-focus);
}
.neu-input[aria-invalid="true"], .neu-select[aria-invalid="true"], .neu-textarea[aria-invalid="true"] { border-color: var(--neu-danger); }
.neu-input:disabled, .neu-select:disabled, .neu-textarea:disabled { cursor: not-allowed; opacity: 0.6; }
```

**Rules:** correct `type` and `autocomplete`; `aria-invalid` + `aria-describedby` for errors; error text names the problem *and* the fix; error and success states use an icon or text in addition to color. Render the error element only when invalid (and reference it in `aria-describedby` only then).

### 5.4 Alerts and notifications

**Types:** success, info, warning, error, neutral, loading, session-expiring, offline.

```html
<div class="neu-alert neu-alert--success" role="status">
  <span class="neu-alert__icon" aria-hidden="true">✓</span>
  <div>
    <h2 class="neu-alert__title">Saved successfully</h2>
    <p class="neu-alert__message">Your project settings have been updated.</p>
  </div>
  <button type="button" class="neu-button neu-button--icon" aria-label="Dismiss notification">×</button>
</div>
```

```css
.neu-alert { display: flex; align-items: flex-start; gap: var(--neu-space-3); border-radius: var(--neu-radius-md);
  padding: var(--neu-space-4); background: var(--neu-surface); box-shadow: var(--neu-shadow-sm); border-left: 4px solid var(--neu-border-strong); }
.neu-alert--success { border-left-color: var(--neu-success); }
.neu-alert--warning { border-left-color: var(--neu-warning); }
.neu-alert--error   { border-left-color: var(--neu-danger); }
.neu-alert--info    { border-left-color: var(--neu-info); }
.neu-alert__title, .neu-alert__message { margin: 0; }
.neu-alert__title { font-weight: 800; }
.neu-alert__message { margin-top: 4px; color: var(--neu-text-secondary); }
```

**Rules:** `role="status"` for non-urgent, `role="alert"` for urgent errors; each severity has a distinct icon and title text; do not auto-dismiss too quickly (>= 6s, longer for errors, never for actions required); provide dismiss when appropriate; keep messages short and actionable.

---

## 6. Navigation, modals, tables, charts, empty states

### 6.1 Navigation

**Patterns:** top bar, sidebar, bottom mobile nav, tabs, breadcrumbs, menus, command menu, pagination.

```html
<nav class="neu-sidebar" aria-label="Primary navigation">
  <a href="/dashboard" class="neu-nav-link" aria-current="page"><span aria-hidden="true">⌂</span><span>Dashboard</span></a>
  <a href="/projects" class="neu-nav-link"><span aria-hidden="true">▣</span><span>Projects</span></a>
  <a href="/settings" class="neu-nav-link"><span aria-hidden="true">⚙</span><span>Settings</span></a>
</nav>
```

```css
.neu-sidebar { display: grid; gap: var(--neu-space-3); width: var(--neu-sidebar-width); padding: var(--neu-space-5); background: var(--neu-surface); box-shadow: var(--neu-shadow-md); }
.neu-nav-link { display: flex; min-height: 44px; align-items: center; gap: var(--neu-space-3); border-radius: var(--neu-radius-md);
  padding: 12px 14px; color: var(--neu-text-secondary); text-decoration: none;
  transition: color var(--neu-duration-normal) var(--neu-ease-standard), box-shadow var(--neu-duration-normal) var(--neu-ease-standard); }
.neu-nav-link:hover { color: var(--neu-text); }
.neu-nav-link[aria-current="page"] { color: var(--neu-accent-text); font-weight: 700; box-shadow: var(--neu-shadow-inset); border-left: 4px solid var(--neu-accent); }
```

**Rules:** `<nav>` with a label when there are several; links for routes, buttons for opening menus; `aria-current="page"` on the active route; the active state needs a non-shadow cue (bold weight, accent bar, or marker); Escape closes menus; focus order matches visual order; mobile nav targets >= 44px.

### 6.2 Modals and dialogs

Prefer native `<dialog>` (or the project's tested dialog library). Never build a CSS-only modal.

```html
<dialog class="neu-dialog" aria-labelledby="dialog-title">
  <div class="neu-dialog__header">
    <h2 id="dialog-title">Delete project?</h2>
    <button type="button" class="neu-button neu-button--icon" aria-label="Close dialog" data-close>×</button>
  </div>
  <div class="neu-dialog__body"><p>This action cannot be undone. All project data will be removed.</p></div>
  <div class="neu-dialog__footer">
    <button type="button" class="neu-button neu-button--secondary" data-close>Cancel</button>
    <button type="button" class="neu-button neu-button--danger">Delete project</button>
  </div>
</dialog>
```

```css
.neu-dialog { width: min(92vw, 560px); max-height: 90dvh; overflow: auto; border: 0; border-radius: var(--neu-radius-lg);
  padding: var(--neu-space-6); background: var(--neu-surface); color: var(--neu-text); box-shadow: var(--neu-shadow-lg); }
.neu-dialog::backdrop { background: rgba(25, 32, 45, 0.45); backdrop-filter: blur(4px); }
.neu-dialog__header, .neu-dialog__footer { display: flex; align-items: center; justify-content: space-between; gap: var(--neu-space-4); }
.neu-dialog__body { margin: var(--neu-space-6) 0; }
```

```js
trigger.addEventListener("click", () => dialog.showModal());          // focus moves in, background inert
dialog.querySelectorAll("[data-close]").forEach(b => b.addEventListener("click", () => dialog.close()));
// Native <dialog> closes on Escape and restores focus to the trigger.
```

**Rules:** accessible name; visible close action; focus enters and returns to the trigger; Escape closes (except where data loss is at stake); long content scrolls; destructive actions are explicit and never the default-focused button; use `role="alertdialog"` only for urgent confirmations; on phones consider a bottom-sheet variant with safe-area padding.

### 6.3 Tables

```html
<div class="neu-table-wrapper" role="region" aria-labelledby="t-cap" tabindex="0">
  <table class="neu-table">
    <caption id="t-cap" class="sr-only">Recent projects</caption>
    <thead>
      <tr>
        <th scope="col" aria-sort="ascending"><button type="button" class="neu-table__sort">Project <span aria-hidden="true">↑</span></button></th>
        <th scope="col">Status</th><th scope="col">Updated</th><th scope="col">Actions</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <th scope="row">AI Study Planner</th>
        <td><span class="neu-badge neu-badge--success"><span aria-hidden="true">●</span> Active</span></td>
        <td>Today</td>
        <td><button type="button" class="neu-button neu-button--secondary">Open</button></td>
      </tr>
    </tbody>
  </table>
</div>
```

```css
.neu-table-wrapper { overflow-x: auto; border-radius: var(--neu-radius-lg); background: var(--neu-surface); box-shadow: var(--neu-shadow-md); }
.neu-table { width: 100%; min-width: 680px; border-collapse: collapse; }
.neu-table th, .neu-table td { padding: 16px; text-align: left; }
.neu-table thead { color: var(--neu-text-secondary); font-size: var(--neu-font-size-sm); }
.neu-table tbody tr { border-top: 1px solid var(--neu-border); }
.neu-table td.num, .neu-table th.num { text-align: right; font-variant-numeric: tabular-nums; }
.neu-table__sort { display: inline-flex; align-items: center; gap: 8px; padding: 0; background: transparent; color: inherit; cursor: pointer; font-weight: 700; }
```

**Rules:** one raised outer container, flat rows (no per-row shadows); sortable headers are buttons with `aria-sort`; statuses use text + icon; right-align numerics; paginate large datasets; give a mobile strategy (stacked card layout or a labeled horizontal scroll region); design loading (skeleton rows), empty, and error states; test long cell content.

### 6.4 Charts

**Types:** line, bar, area, donut, progress, sparkline, heatmap, timeline.

```css
.neu-chart-card { min-height: 320px; border-radius: var(--neu-radius-lg); padding: var(--neu-space-6); background: var(--neu-surface); box-shadow: var(--neu-shadow-md); }
.neu-chart-card__canvas { min-height: 240px; }
```

**Rules:** pick the chart type for the question being answered; keep the chart area flat inside its card; add a legend, axis labels, and a **text summary** ("Sales rose 12% week over week, peaking Thursday"); distinguish series with patterns, markers, line styles, or direct labels in addition to color; never hide critical data in hover-only tooltips (make data keyboard-reachable or provide a table fallback); no 3D effects; accuracy beats softness; supply loading, empty, and error states.

### 6.5 Empty states

**Types:** first use, no results, no notifications, completed, permission-limited, offline, failed load.

```html
<section class="neu-empty-state" aria-labelledby="empty-title">
  <div class="neu-empty-state__icon" aria-hidden="true">✦</div>
  <h2 id="empty-title">No projects yet</h2>
  <p>Create your first project to start organizing your work.</p>
  <button type="button" class="neu-button neu-button--primary">Create project</button>
</section>
```

```css
.neu-empty-state { display: grid; justify-items: center; gap: var(--neu-space-4); border-radius: var(--neu-radius-lg);
  padding: var(--neu-space-9) var(--neu-space-6); text-align: center; background: var(--neu-surface); box-shadow: var(--neu-shadow-md); }
.neu-empty-state__icon { display: grid; width: 64px; height: 64px; place-items: center; border-radius: 50%;
  color: var(--neu-accent-text); font-size: 28px; box-shadow: var(--neu-shadow-inset); }
.neu-empty-state h2, .neu-empty-state p { margin: 0; }
.neu-empty-state p { max-width: 440px; color: var(--neu-text-secondary); }
```

**Rules:** explain *why* it is empty, never blame the user, offer a next step, keep decorative art `aria-hidden`, use a real heading.

---

## 7. Page patterns

### 7.1 Dashboards

Structure: header, navigation, page title, filters/date range, summary metrics, primary chart, secondary widgets, activity feed, alerts, quick actions.

1. Identify the user's main goal; put the most important information first.
2. Use a 12-column responsive grid and cards for related metrics.
3. Show *change* (delta, trend, comparison), not only raw numbers.
4. Keep large data areas flat; use whitespace to separate groups.
5. Provide loading, empty, and error states for every widget.
6. Provide chart summaries and handle long labels.

```css
.neu-dashboard { display: grid; grid-template-columns: var(--neu-sidebar-width) minmax(0, 1fr); min-height: 100vh; background: var(--neu-bg); }
.neu-dashboard__main { min-width: 0; padding: var(--neu-space-7); }
.neu-dashboard__grid { display: grid; grid-template-columns: repeat(12, minmax(0, 1fr)); gap: var(--neu-space-6); }
.neu-dashboard__metric { grid-column: span 3; }
.neu-dashboard__primary { grid-column: span 8; }
.neu-dashboard__secondary { grid-column: span 4; }
@media (max-width: 960px) {
  .neu-dashboard { grid-template-columns: 1fr; }
  .neu-dashboard__metric, .neu-dashboard__primary, .neu-dashboard__secondary { grid-column: span 12; }
}
```

### 7.2 Forms

1. State the form's purpose; group related fields in `<fieldset>` + `<legend>`.
2. Visible labels, correct `type`, `autocomplete`, and `aria-describedby`.
3. Mark required fields clearly (text such as "(required)" or an asterisk explained once).
4. Validate on blur/submit; show errors beside fields **and** an error summary at the top for multiple errors (move focus to the summary on failed submit).
5. **Preserve valid input** after a failed submission; explain how to fix each error.
6. Disable duplicate submissions; show a submitting state; show success feedback; surface server errors.
7. Support: initial, focused, filled, invalid, valid, submitting, success, server error, disabled, session-expired.
8. Multi-step forms: show progress ("Step 2 of 4"), allow going back, keep the primary action in a stable position.

```css
.neu-form { display: grid; gap: var(--neu-space-6); }
.neu-form__section { display: grid; gap: var(--neu-space-4); border: 0; padding: 0; margin: 0; }
.neu-form__legend { margin-bottom: var(--neu-space-3); font-size: var(--neu-font-size-lg); font-weight: 800; }
.neu-form__grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: var(--neu-space-4); }
```

### 7.3 Authentication pages

**Screens:** login, registration, forgot/reset password, email verification, OTP, 2FA, recovery, session expired, access denied.

```html
<main class="neu-auth-page">
  <section class="neu-auth-card" aria-labelledby="login-title">
    <header class="neu-auth-card__header">
      <p class="neu-auth-card__brand">StudyFlow</p>
      <h1 id="login-title">Welcome back</h1>
      <p>Sign in to continue to your dashboard.</p>
    </header>
    <form class="neu-form">
      <div class="neu-field">
        <label for="login-email" class="neu-field__label">Email address</label>
        <input id="login-email" name="email" type="email" class="neu-input" autocomplete="email" required />
      </div>
      <div class="neu-field">
        <label for="login-password" class="neu-field__label">Password</label>
        <input id="login-password" name="password" type="password" class="neu-input" autocomplete="current-password" required />
      </div>
      <button type="submit" class="neu-button neu-button--primary">Sign in</button>
    </form>
  </section>
</main>
```

**UX:** one primary task per page; centered responsive card; password visibility toggle (a labeled button with `aria-pressed`); loading state; link to recovery and the alternate flow. OTP inputs: `inputmode="numeric"`, `autocomplete="one-time-code"`, and paste support.

**Security:** never log passwords or OTPs; use generic auth errors ("Email or password is incorrect"); do not reveal whether an email exists during recovery; do not store credentials or tokens in `localStorage`; use secure, HTTP-only session handling; never render tokens in the UI; always offer an account-recovery path.

### 7.4 Mobile layout

Build mobile-first, use content-driven breakpoints, and test portrait and landscape.

```css
.neu-page { width: min(100% - 32px, var(--neu-content-width)); margin-inline: auto; padding-block: var(--neu-space-6); }
@media (min-width: 768px) { .neu-page { width: min(100% - 64px, var(--neu-content-width)); padding-block: var(--neu-space-8); } }

.neu-mobile-bottom-nav { padding-bottom: max(12px, env(safe-area-inset-bottom)); }

@media (max-width: 600px) {
  :root { --neu-radius-lg: 20px; }   /* soften oversized radii that waste space */
}
```

**Rules:** one-column layouts when needed; no hover-only information; no horizontal page scroll; keep primary actions visible; keep shadows subtle on small screens (reduce offsets/blur for performance); touch targets >= 44px; use `dvh` for full-height layouts; support safe-area insets; test 200% zoom and large system text.

---

## 8. Motion

Motion gives tactile feedback; it must never carry meaning on its own.

```css
.neu-interactive {
  transition: transform var(--neu-duration-normal) var(--neu-ease-standard),
              box-shadow var(--neu-duration-normal) var(--neu-ease-standard),
              background-color var(--neu-duration-normal) var(--neu-ease-standard),
              color var(--neu-duration-normal) var(--neu-ease-standard);
}
.neu-interactive:hover  { transform: translateY(-1px); }
.neu-interactive:active { transform: translateY(1px); box-shadow: var(--neu-shadow-inset); }
```

**Rules:** animate `transform`, `opacity`, and `box-shadow` only (never layout properties); keep durations 120-280ms; do not animate everything at once or animate large surfaces; avoid infinite animation except for loading indicators; page transitions stay short; the reduced-motion block in section 3 must be present; every animated state also has a static equivalent; test on low-powered devices.

---

## 9. Accessibility

Neumorphism's known weaknesses are **low edge contrast** and **shadow-only affordances**. Compensate deliberately.

### Checklist

**Structure:** logical heading order; one `<main>`; `<nav>`, `<button>`, `<a>`, `<form>`, `<fieldset>`/`<legend>` used correctly; tables only for tabular data; native semantics before ARIA.

**Keyboard:** everything reachable and operable; focus order follows visual order; menus and dialogs manage focus; Escape closes temporary layers; no keyboard traps.

**Visual:** text 4.5:1 (3:1 large); control boundaries and focus rings >= 3:1; placeholders are not labels; status and focus never rely on color or shadow alone; disabled states are understandable; important boundaries stay visible.

**Motion:** reduced-motion respected; no motion-only information.

**Screen readers:** accessible names for icon-only controls; errors tied to fields; live regions (`role="status"` / `role="alert"`) for dynamic updates; announce sort changes and async results.

### Test protocol

1. Keyboard-only walkthrough of every flow.
2. 200% browser zoom and 320px width (WCAG reflow).
3. **Shadows-off test:** set `*{box-shadow:none!important}` and confirm every control, state, and boundary is still identifiable.
4. Forced-colors / high-contrast mode (`@media (forced-colors: active)`): ensure borders and outlines remain.
5. Contrast check for text, muted text, borders, focus, and disabled states in each theme.
6. A screen reader pass where possible.

```css
@media (forced-colors: active) {
  .neu-button, .neu-input, .neu-card { border: 1px solid CanvasText; box-shadow: none; }
  .neu-nav-link[aria-current="page"] { outline: 2px solid Highlight; }
}
```

---

## 10. Theme generator

When the user asks for a theme from a brand color, mood, or mode:

**Gather or infer:** brand color, light/dark, mood (calm, professional, academic, creative, futuristic, minimal, energetic, medical, financial, productivity), contrast target, framework, styling system, component scope, desktop/mobile.

**Procedure:**

1. Parse the brand color; derive hover, soft, and on-accent colors.
2. Choose a neutral surface (a slightly tinted gray; mid-light surfaces give the best shadow range).
3. Derive text, secondary, and muted text colors that meet 4.5:1 on the surface.
4. Derive shadow-light (lighter than surface) and shadow-dark (darker than surface) from the surface hue.
5. Generate distinguishable semantic colors that are text-safe on the surface.
6. Emit the full token set (section 3) plus button, card, and input samples.
7. Check contrast and list any failures or compromises.

**Output format:**

```md
# Generated Neumorphic Theme

## Summary
Mode · Mood · Primary · Surface · Framework · Accessibility notes

## CSS variables
(complete token block)

## Component samples
Button · Card · Input

## Accessibility warnings
(contrast ratios that are borderline or fail, and what was done about them)

## Usage
1. Import the tokens.
2. Apply the component classes.
3. Verify with the shadows-off test.
```

**Rules:** do not pick colors for looks alone; avoid making everything pastel; keep semantic colors distinguishable (including for color-blind users); keep the system easy to customize; state limitations plainly.

---

## 11. Code review

Use when reviewing existing neumorphic code. **Do not modify code during a review unless asked.**

**Review order:** project structure and conventions -> shared tokens -> component reuse -> semantic HTML -> keyboard support -> focus visibility -> text and UI-boundary contrast -> shadow-only state cues -> responsive behavior -> reduced motion -> loading/empty/error states -> performance -> maintainability -> security-sensitive code.

**Output format:**

```md
# Neumorphic Code Review

## Critical issues
- File / Location / Problem / Impact / Recommended fix

## Accessibility issues
(same fields)

## Functional issues
## Visual consistency issues
## Responsive issues
## Performance issues

## Positive findings
- ...

## Suggested fix order
1. Highest priority
2. ...
```

**Rules:** give exact file names and line numbers; separate critical from optional; prefer reusable fixes (token or component-level) over one-off patches; explain the reason behind each recommendation; do not demand a rewrite when a targeted fix works.

---

## 12. Framework adapters

Always follow the project's existing conventions first; the notes below apply when there is nothing to follow.

**React** — functional components; variants via props; spread props last but never override accessibility-critical attributes by accident.

```jsx
export function NeuButton({ children, variant = "secondary", type = "button", className = "", ...props }) {
  return (
    <button type={type} className={`neu-button neu-button--${variant} ${className}`.trim()} {...props}>
      {children}
    </button>
  );
}
```

**Next.js** — server components by default; add `"use client"` only for state/events; never touch `window` during SSR; avoid theme flash with the inline head script; use framework routing and image features.

**Vue** — single-file components, semantic templates, `aria-*` bindings, minimal watchers, shared tokens.

**Angular** — standalone components, Angular forms for validation, avoid direct DOM manipulation, scoped styles with global tokens.

**Tailwind** — put tokens in `tailwind.config` and map them to CSS variables so dark mode still works; extract repeated class strings into components or `@apply` layers.

```js
export default {
  theme: {
    extend: {
      colors: { neu: { bg: "var(--neu-bg)", surface: "var(--neu-surface)", text: "var(--neu-text)", accent: "var(--neu-accent)" } },
      boxShadow: {
        "neu-sm": "var(--neu-shadow-sm)",
        "neu-md": "var(--neu-shadow-md)",
        "neu-inset": "var(--neu-shadow-inset)",
      },
    },
  },
};
```

**Plain CSS / CSS Modules** — custom properties in a global file; BEM or the existing naming scheme; component-scoped styles; shallow selectors; do not redeclare global tokens inside modules.

**Component libraries (MUI, Chakra, etc.)** — map tokens into the library's theme object rather than overriding styles ad hoc.

---

## 13. Project scaffolding

Use for new projects or when adding a reusable system to an existing app.

```text
src/
├── components/
│   ├── ui/        NeuButton/ NeuCard/ NeuInput/ NeuModal/ NeuAlert/ NeuBadge/ NeuTable/
│   └── layout/    Header/ Sidebar/ PageContainer/
├── styles/        tokens.css  reset.css  utilities.css  themes.css
├── hooks/         useTheme  useMediaQuery
├── pages/         Dashboard/ Login/ Settings/
└── tests/         components/ accessibility/
```

**Workflow:** inspect -> identify framework, package manager, and any design system -> add tokens, reset, themes -> build foundational components -> add example pages -> add accessibility tests (and visual regression if available) -> run tests and formatter -> document.

**Docs to create:** `README.md`, `DESIGN_TOKENS.md`, `ACCESSIBILITY.md`, `COMPONENTS.md`, `CONTRIBUTING.md`.

**Rules:** no unnecessary dependencies; use the existing package manager; centralize tokens; give every public component an example; keep components independently testable; document breaking changes.

---

## 14. Final quality checklist

**Design system**
- [ ] Background, surface, text, accent, semantic, border, and focus tokens exist
- [ ] Shadow levels, radii, spacing, and typography are tokenized and consistent
- [ ] No raw shadow/color values inside components

**Components**
- [ ] Reusable, follow project conventions, no duplication
- [ ] All required states exist: default, hover, active, focus, disabled, loading, success, error
- [ ] Tested with realistic (long, short, empty) content

**Accessibility**
- [ ] Semantic HTML; keyboard operable; visible focus
- [ ] Contrast: text 4.5:1, boundaries/focus 3:1
- [ ] Labels visible; errors associated; icon-only controls named
- [ ] Status not conveyed by color or shadow alone (shadows-off test passes)
- [ ] Reduced motion, zoom, forced-colors handled; dialog focus managed

**Responsive**
- [ ] Works at 320-360px, tablet, desktop, landscape
- [ ] 44px touch targets; no horizontal overflow; tables and dialogs adapt
- [ ] Long text does not break layout

**States**
- [ ] Loading, empty, error, success, disabled, offline (where relevant)

**Performance**
- [ ] No needless animation; limited blur; moderate shadow sizes on mobile
- [ ] Images optimized; no layout shift; effects tested on low-powered devices

**Code quality**
- [ ] Formatted, linted, type-checked, tests pass
- [ ] Docs updated; unrelated files untouched; no secrets or tokens exposed

---

## 15. Anti-patterns

- Every element has a large raised shadow, so hierarchy disappears.
- Inputs or buttons with **no border and no contrast**, identifiable only by shadow.
- Using `--neu-text-muted` or pastel semantic colors for body text without checking contrast.
- Active nav or selected tab distinguished only by an inset shadow.
- Removing `outline` and relying on a shadow change for focus.
- Placeholder as the only label.
- Clickable `<div>` or `<span>` instead of `<button>`/`<a>`.
- Hover-only tooltips holding essential data.
- Heavy `backdrop-filter` and huge blurs on mobile.
- Reusing light-theme shadows unchanged in dark mode.
- Rewriting the whole app when a token or component change would do.

---

## 16. Response format

When delivering work with this skill:

1. One short paragraph: what you built or changed and the key decisions.
2. The code or file changes (follow the project's structure).
3. A brief **accessibility and limitations** note: contrast trade-offs, anything untested, follow-ups.
4. If you ran checks, say which ones and what they returned. If you could not run them, say so.

---

## Starter prompt

```text
Use the neumorphism-ui-kit skill. Inspect the project, identify the framework and
styling system, reuse existing tokens and components, apply the relevant sections,
keep state indicators independent of shadows, support keyboard, mobile, and reduced
motion, avoid touching unrelated files, then summarize changes and limitations.
```
