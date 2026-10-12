---
name: optimus-ui-theming
description: Use whenever the task is about how Optimus UI (@openng/optimus-ui, PrimeNG successor) LOOKS - theme presets (Aura, Material, Lara, Nora), definePreset, design tokens (primitive/semantic/component), primary and surface colors, dark mode toggle and darkModeSelector, focus ring, CSS variables (--p-*), scoped [dt] tokens, CSS layers (cssLayer), Tailwind CSS integration (@openng/optimus-ui-tailwindcss, v3 and v4), overriding component styles, pass-through ([pt], ptOptions, global pt), unstyled mode, rem scaling, RTL. Trigger even when the user just says "change the color", "make it dark", "restyle the buttons", "Tailwind classes don't override the component", "thème", "mode sombre", or "personnaliser le style". Prefer this skill over writing CSS overrides or ::ng-deep.
---

# Optimus UI — theming and styling

Optimus UI is design-agnostic: components carry no hard-coded look. A **preset** (design tokens) feeds a **base** (CSS using variables), so you restyle by editing tokens in TypeScript, not by fighting CSS. Verified against the official docs (Optimus UI 2.0.2, 2026-10-12). Core usage rules live in the `optimus-ui` skill.

## Decision ladder — pick the lightest tool that works

1. **Global look** (brand color, surfaces, radius, focus ring, form fields) → `definePreset(Aura, { semantic: … })`.
2. **One component type everywhere** (all cards, all buttons) → `definePreset(Aura, { components: { card: … } })`.
3. **One instance only** → the scoped `[dt]` input (preferred over `::ng-deep`).
4. **Structure/attributes of internals** (extra classes, aria, data-*, listeners on inner DOM nodes) → pass-through `[pt]` (or global `pt` in `provideOptimus`).
5. **Utility-class layout and spacing** → Tailwind with `@openng/optimus-ui-tailwindcss`.
6. **Total control** → `[unstyled]` / unstyled mode with your own classes.
7. Plain CSS overrides / `!important` → last resort, and only after enabling `cssLayer`.

Rule of thumb from the docs: use primitives for the palette, semantic tokens for shared design decisions (primary, surface, focus ring), component tokens only for a specific component. Overriding with style classes is not best practice.

## Setup

```typescript
import { provideOptimus } from '@openng/optimus-ui/config';
import { definePreset } from '@openng/optimus-ui-themes';
import Aura from '@openng/optimus-ui-themes/aura';

const MyPreset = definePreset(Aura, {
  semantic: {
    primary: { 50: '{indigo.50}', 100: '{indigo.100}', 200: '{indigo.200}', 300: '{indigo.300}', 400: '{indigo.400}',
               500: '{indigo.500}', 600: '{indigo.600}', 700: '{indigo.700}', 800: '{indigo.800}', 900: '{indigo.900}', 950: '{indigo.950}' }
  }
});

providers: [provideOptimus({ theme: { preset: MyPreset, options: { prefix: 'p', darkModeSelector: '.app-dark', cssLayer: false } } })]
```

Built-in presets: **Aura** (default look, emerald primary), **Material** (Material Design v2), **Lara** (Bootstrap-like), **Nora** (enterprise-style). Preset sources are the best reference when building your own: `packages/optimus-ui-themes/src/presets` in the GitHub repo.

Theme `options`:

| Option | Default | Meaning |
|---|---|---|
| `prefix` | `'p'` | CSS variable prefix → `var(--p-primary-color)` |
| `darkModeSelector` | `'system'` | `'system'` = `prefers-color-scheme`; a class like `'.app-dark'` makes dark mode toggleable; `false`/`'none'` disables dark mode |
| `cssLayer` | `false` | `{ name: 'optimus', order: '…' }` wraps library CSS in a cascade layer so your unlayered CSS (or Tailwind utilities) wins |

## The pitfall that wastes the most time: `colorScheme`

If the preset defines a token under `colorScheme` (light/dark) and you override it with a plain value, **your override is silently ignored** — `colorScheme` takes precedence. Always keep the same structure as the original preset (direct vs `colorScheme`) and set both light and dark values. Check the preset source first (e.g. Aura defines `highlight` under `colorScheme`).

```typescript
// ❌ ignored in Aura
definePreset(Aura, { semantic: { highlight: { background: '{primary.50}', color: '{primary.700}' } } });
// ✅
definePreset(Aura, { semantic: { colorScheme: {
  light: { highlight: { background: '{primary.50}',  color: '{primary.700}' } },
  dark:  { highlight: { background: '{primary.200}', color: '{primary.900}' } } } } });
```

Reserved keys that cannot be token names: `primitive, semantic, components, directives, colorscheme, light, dark, common, root, states, extend`.

## Dark mode

Set `darkModeSelector` to a class, then toggle that class on `<html>`:

```typescript
provideOptimus({ theme: { preset: Aura, options: { darkModeSelector: '.app-dark' } } });
toggleDark() { document.documentElement.classList.toggle('app-dark'); }
```

Persist the user's choice yourself (guard `localStorage`/`matchMedia` for SSR). If Tailwind is used, align its dark variant with the same selector (see `references/tailwind.md`). Always-dark: put the class on `<html>` once.

## Runtime theme changes

`usePreset(preset)` replaces the preset; `updatePreset({...})` merges tokens; `updatePrimaryPalette({...})`, `updateSurfacePalette({...})` (optionally `{ light: … }` / `{ dark: … }`); `$dt('primary.color')` reads token info; `palette('#10b981')` or `palette('{blue}')` generates a 50–950 ramp. All from `@openng/optimus-ui-themes`.

## Scoped tokens (`[dt]`)

```html
<p-toggleswitch [(ngModel)]="on" [dt]="amberSwitch" />
```
```typescript
amberSwitch = {
  colorScheme: {
    light: { root: { checkedBackground: '{amber.500}', checkedHoverBackground: '{amber.600}' } },
    dark:  { root: { checkedBackground: '{amber.400}', checkedHoverBackground: '{amber.300}' } }
  }
};
```

Token names for a component (e.g. `button.primary.background` → `--p-button-primary-background`) are listed at the bottom of its `.md` doc page (see `optimus-ui/references/live-docs.md`).

## CSS hygiene

- **Layers**: enable `cssLayer` to make overrides painless; wrap a CSS reset in a lower layer (`@layer reset, optimus;`) so it cannot break components.
- **Scale**: components use `rem`; adjust `html { font-size }` to scale everything.
- **Fonts**: components inherit the app's font; there is no font token.
- **Specificity fights**: prefer a token or `[dt]` first; never `::ng-deep` into internals when `[pt]` can target the node.

## More recipes and integrations

- `references/tokens-recipes.md` — primary/surface palettes, "noir" mode, form fields, focus ring, per-component overrides, `extend` (custom tokens/CSS).
- `references/tailwind.md` — Tailwind v3/v4 plugin, utilities, dark mode alignment, override strategies, animations.
- `references/passthrough-and-unstyled.md` — `[pt]` syntax, `pc` prefix, hooks, global `pt`, merge options, unstyled mode.
