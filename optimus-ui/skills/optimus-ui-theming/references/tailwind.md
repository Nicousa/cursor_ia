# Tailwind CSS integration

The core of Optimus UI does not depend on Tailwind. The plugin `@openng/optimus-ui-tailwindcss` (one npm package: CSS build for Tailwind **v4**, JS build for **v3**) exposes Optimus tokens as utilities and works in styled and unstyled modes. This replaces PrimeFlex, which is not maintained here.

```shell
npm i @openng/optimus-ui-tailwindcss
```

## Enable

**Tailwind v4** — in the CSS file with the Tailwind import:
```css
@import "tailwindcss";
@import "@openng/optimus-ui-tailwindcss";
```

**Tailwind v3** — `tailwind.config.js`:
```javascript
import OptimusUiTailwind from '@openng/optimus-ui-tailwindcss';
export default { plugins: [OptimusUiTailwind] };
```

## Utilities added

All variants/breakpoints work (e.g. `dark:sm:hover:bg-primary`).

| Class | Meaning |
|---|---|
| `primary-[50-950]`, `surface-[0-950]` | Palettes (e.g. `bg-primary-500`, `text-surface-700`) |
| `primary`, `primary-contrast`, `primary-emphasis` | Default primary, its contrast and emphasis colors |
| `border-surface` | Surface-colored border |
| `bg-emphasis`, `bg-highlight`, `bg-highlight-emphasis` | Hover/emphasis and highlight backgrounds |
| `rounded-border` | The theme's border radius |
| `text-color`, `text-color-emphasis` | Body text colors |
| `text-muted-color`, `text-muted-color-emphasis` | Secondary text |

Animation utilities (for `styleclass` and `animateonscroll` directives): `animate-fadein/fadeout/slidedown/slideup/scalein/fadeinleft/fadeinright/fadeinup/fadeindown/zoomin…/flip…`, plus `animate-duration-*`, `animate-delay-*`, `animate-infinite|once|twice`, `animate-ease-*`, `animate-fill-*`, `animate-running|paused`, direction and backface utilities.

## Dark mode alignment

If Optimus uses a class selector (say `.my-app-dark`), Tailwind's `dark:` variant must use the same one:

```css
/* v4 */
@import "tailwindcss";
@import "@openng/optimus-ui-tailwindcss";
@custom-variant dark (&:where(.my-app-dark, .my-app-dark *));
```
```javascript
// v3
export default { darkMode: ['selector', '[class~="my-app-dark"]'], plugins: [OptimusUiTailwind] };
```
With the default `darkModeSelector: 'system'` no extra config is needed.

## Making utilities override component styles

Utilities can lose to component CSS on specificity. Two solutions, in order of preference:

**1. CSS layer (recommended)** — put Optimus in a layer *below* utilities.

```typescript
// Tailwind v4 — optimus layer after theme & base, before utilities. No CSS change needed.
provideOptimus({ theme: { preset: Aura, options: { cssLayer: { name: 'optimus', order: 'theme, base, optimus' } } } })

// Tailwind v3
provideOptimus({ theme: { preset: Aura, options: { cssLayer: { name: 'optimus', order: 'tailwind-base, optimus, tailwind-utilities' } } } })
```
```css
/* v3 only — native @layer is not used by Tailwind v3, declare it */
@layer tailwind-base, optimus, tailwind-utilities;
@layer tailwind-base { @tailwind base; }
@layer tailwind-utilities { @tailwind components; @tailwind utilities; }
```

**2. Important modifier (last resort)** — v4: `class="p-8!"`; v3: `class="!p-8"`.

## Patterns

- Layout/spacing with Tailwind, look-and-feel with tokens: `<div class="flex flex-col gap-2 w-full sm:w-80"><label for="n">Name</label><input pInputText id="n" /></div>`.
- Prefer `fluid` (component input or `<p-fluid>`) over `w-full` hacks for form controls.
- Tailwind classes inside `[pt]` target inner DOM nodes: `[pt]="{ header: 'bg-primary text-primary-contrast' }"`.
- Migrating from PrimeFlex: classes like `grid`, `col-6`, `flex justify-content-center` become Tailwind (`grid grid-cols-12`, `col-span-6`, `flex justify-center`). See the PrimeFlex guide on the docs site (`/guides/primeflex`).
