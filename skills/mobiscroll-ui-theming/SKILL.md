---
name: mobiscroll-ui-theming
description: |
  Mobiscroll theming patterns — setOptions global theme, CSS variables, Sass customization.
  Invoked by mobiscroll-ui for theming questions. Framework-agnostic.
  Do not auto-trigger — invoked by mobiscroll-ui only.
---

# Mobiscroll — Theming

> This skill is loaded by `mobiscroll-ui`. If you arrived here directly, invoke
> `mobiscroll-ui` first to run environment detection and the mandatory schema lookup.

The theming system is framework-agnostic — the same themes and CSS variables work everywhere.

> **Theming and the MCP tools:** theming *options* are modeled — `theme`, `themeVariant`,
> `cssClass`, `colors`, `inputStyle`, `labelStyle` are component options in the
> `getComponentSchema` MCP tool, and theme demos are searchable via the `searchExamples`
> MCP tool; verify those there. What the MCP does **not** model is the set of **CSS
> custom-property and Sass variable names** (e.g. `$mbsc-ios-accent`) — names shown in this
> skill are illustrative; verify the exact names against the docs below and do **not**
> invent them.

## Global Theme Application (Recommended)

Set the theme once at app startup using `setOptions()`. This applies to **all** Mobiscroll
components in the app — no per-component prop needed.

```javascript
// JS / jQuery
import * as mobiscroll from '@mobiscroll/javascript'; // or '@mobiscroll/jquery'
mobiscroll.setOptions({
  theme: 'ios',         // 'ios' | 'material' | 'windows'
  themeVariant: 'auto'  // 'light' | 'dark' | 'auto'
});
```

```typescript
// React / Angular / Vue — named import
import { setOptions } from '@mobiscroll/react';   // or /angular or /vue
setOptions({
  theme: 'material',
  themeVariant: 'dark'
});
```

Call this before any component renders (e.g., in `main.ts`, `App.tsx`, or `main.js`).
A `theme` prop on an individual component overrides the global setting for that instance.

## Built-in Themes

- `ios` — iOS-style look and feel
- `material` — Material Design
- `windows` — Windows/Fluent UI style

Each theme has light and dark variants controlled by `themeVariant`:

```
theme: 'ios',
themeVariant: 'dark'   // 'light' | 'dark' | 'auto'
```

`'auto'` follows the operating system preference.

## CSS Variables

Mobiscroll exposes CSS variables for customization. Override them in your CSS to change
colors, fonts, and spacing without modifying Mobiscroll source files.

For the full list of available CSS variables per theme, consult the online docs:
`https://mobiscroll.com/docs/docs/{framework}/theming/sass-themes.md`

Replace `{framework}` with `javascript`, `react`, `angular`, `vue`, or `jquery`.

## Sass Customization

```scss
// Override variables before import
$mbsc-ios-accent: #e97d23;
$mbsc-material-accent: #4caf50;

// webpack — use ~ prefix:
@import '~@mobiscroll/{package}/dist/css/mobiscroll.scss';

// Vite / modern bundlers — use direct path (no ~ prefix):
@import '@mobiscroll/{package}/dist/css/mobiscroll.scss';
```

Replace `{package}` with the framework package name (`react`, `angular`, `vue`,
`javascript`, `jquery`). The `~` prefix is a webpack-specific resolver convention
and is not supported by Vite or other modern bundlers — use the bare package path instead.

## Component-Level Styling

Use `cssClass` (or the equivalent prop) to add custom CSS classes to a component:

```
cssClass: 'my-custom-calendar'
```

Then style `.my-custom-calendar` in your CSS. This is the recommended approach for
component-specific styling that doesn't affect the global theme.

For detailed theming, available CSS variables, Sass variable lists, and advanced
customization, fetch the theming docs from:
`https://mobiscroll.com/docs/docs/{framework}/theming/sass-themes.md`
