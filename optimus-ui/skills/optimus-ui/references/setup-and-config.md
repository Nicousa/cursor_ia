# Setup and global configuration

Contents: prerequisites · packages · manual install · icons · provideOptimus options · runtime config · locale (French) · z-index · CSP · filter modes · scale

## Prerequisites

- Angular v21+ (v1 targets 21, v2 targets 22) including `@angular/cdk`, `@angular/forms`, `@angular/router`
- RxJS ≥ 7.8.1
- npm, yarn or pnpm. Standalone or NgModule workspaces both work.

| Optimus UI | Angular |
|---|---|
| v2 | v22 |
| v1 | v21 |

## Packages (all versioned and released together)

| Package | Role |
|---|---|
| `@openng/optimus-ui` | Components, directives, services — the only one most apps install directly |
| `@openng/optimus-ui-themes` | Presets: Aura, Material, Lara, Nora |
| `@openng/optimus-ui-styled` | Styled-mode theming engine (presets → CSS variables) |
| `@openng/optimus-ui-styles` | Base styles shared by components |
| `@openng/optimus-ui-tailwindcss` | Tailwind plugin (v3 JS + v4 CSS) |
| `@openng/optimus-ui-locale` | i18n locale definitions |
| `@openng/optimus-ui-motion` | Animation/transition utilities |
| `@openng/optimus-ui-utils` | Shared helpers |
| `@openng/icons` | Optional icon set (`pi pi-*` classes) |

## Manual install

```shell
npm install @openng/optimus-ui @openng/optimus-ui-themes      # Angular 22
npm install @openng/optimus-ui@1 @openng/optimus-ui-themes@1   # Angular 21 (pin the major!)
```

```typescript
import { provideOptimus } from '@openng/optimus-ui/config';
import Aura from '@openng/optimus-ui-themes/aura';
providers: [provideOptimus({ theme: { preset: Aura } })]
```

Animations: the installation page shows no animations provider, while the configuration page's example includes `provideAnimationsAsync()`. Do not add it by default; add it only if a component's docs require it or the build/runtime asks for it (verify against the installed Angular version).

## Icons

Components accept any icon through templating, so icons are optional. Defaults (e.g. DatePicker arrows) and every doc example use `pi pi-*` classes.

```shell
npm install @openng/icons
```
```scss
@import "@openng/icons/openng-icons.css";   // global stylesheet, or the "styles" array of angular.json
```

Blank icons almost always mean the stylesheet is not imported. Constants class: `OpenngIcons` (the old `PrimeIcons` still compiles through a deprecated alias).

## provideOptimus options

```typescript
provideOptimus({
  theme: { preset: Aura, options: { prefix: 'p', darkModeSelector: 'system', cssLayer: false } },
  ripple: false,              // opt-in animation for buttons etc.
  inputVariant: 'outlined',   // or 'filled'
  overlayAppendTo: 'self',    // or 'body' — see below
  csp: { nonce: '...' },
  translation: { /* locale keys */ },
  pt: { /* global pass-through, see optimus-ui-theming */ }
})
```

- `overlayAppendTo: 'body'` fixes overlays (select panels, popovers, menus) clipped by `overflow: hidden` ancestors. Default is `'self'`. Individual components also take `appendTo`.
- `inputVariant: 'filled'` gives inputs a background instead of a border; some presets (Material) add further changes per variant.
- Theme `options` are explained in `optimus-ui-theming`.

## Runtime configuration (inject, don't re-provide)

```typescript
import { Optimus } from '@openng/optimus-ui/config';
const config = inject(Optimus);
config.ripple.set(true);
config.setTranslation(frenchKeys);
```

z-index and filter modes live on `OptimusConfig` from `@openng/optimus-ui/api`:

```typescript
import { OptimusConfig } from '@openng/optimus-ui/api';
optimusConfig.zIndex = { modal: 1100, overlay: 1000, menu: 1000, tooltip: 1100 };  // e.g. fixed app header
```

z-indexes are managed automatically; only override when a fixed layout needs a dropdown below the header but a modal above it.

## Locale / translation (French)

Ready-made locales live in `@openng/optimus-ui-locale` (`import { de } from '@openng/optimus-ui-locale/js/de.js'` — check the package for the available languages before importing `fr`). Otherwise pass keys yourself; the DatePicker uses PrimeNG date tokens (`dd/mm/yy`, **not** Angular's `dd/MM/yyyy`):

```typescript
provideOptimus({
  translation: {
    accept: 'Oui', reject: 'Non', choose: 'Choisir', upload: 'Téléverser', cancel: 'Annuler',
    clear: 'Effacer', apply: 'Appliquer', today: 'Aujourd’hui', weekHeader: 'Sem',
    firstDayOfWeek: 1, dateFormat: 'dd/mm/yy',
    dayNames: ['dimanche','lundi','mardi','mercredi','jeudi','vendredi','samedi'],
    dayNamesShort: ['dim','lun','mar','mer','jeu','ven','sam'],
    dayNamesMin: ['D','L','M','M','J','V','S'],
    monthNames: ['janvier','février','mars','avril','mai','juin','juillet','août','septembre','octobre','novembre','décembre'],
    monthNamesShort: ['janv','févr','mars','avr','mai','juin','juil','août','sept','oct','nov','déc'],
    weak: 'Faible', medium: 'Moyen', strong: 'Fort', passwordPrompt: 'Saisissez un mot de passe',
    emptyMessage: 'Aucune option disponible', emptyFilterMessage: 'Aucun résultat', emptySearchMessage: 'Aucun résultat',
    startsWith: 'Commence par', contains: 'Contient', notContains: 'Ne contient pas', endsWith: 'Finit par',
    equals: 'Égal à', notEquals: 'Différent de', noFilter: 'Aucun filtre',
    aria: { close: 'Fermer', previous: 'Précédent', next: 'Suivant' }
  }
})
```

Switching language at runtime: `config.setTranslation(res)` (works with ngx-translate: fetch the `optimus` key and pass it). Other available keys include `fileSizeTypes`, `chooseYear/Month/Date`, `prev*/next*` for date parts, `lt/lte/gt/gte`, `dateIs/dateIsNot/dateBefore/dateAfter`, `matchAll/matchAny`, `addRule/removeRule`, `searchMessage`, `selectionMessage`, `emptySelectionMessage`, and the whole `aria.*` group (paginator, table row selection/editing, carousel, galleria, orderlist/picklist moves…) — localize `aria.*` for accessibility.

## CSP

`provideOptimus({ csp: { nonce } })` sets the nonce used on dynamically generated `<style>` elements.

## Table filter modes

`optimusConfig.filterMatchModeOptions = { text: [FilterMatchMode.STARTS_WITH, ...], numeric: [...], date: [...] }` sets the defaults offered in Table filter menus (`FilterMatchMode` comes from `@openng/optimus-ui/api`).

## Scale

Components use `rem`. Change `html { font-size: 14px; }` to scale the whole library; the docs site itself uses 14px.

## SSR

The official documentation site is an Angular SSR/prerendered app built on Optimus UI, so SSR is supported. Guard browser-only code (DOM measurement, `localStorage` for dark mode) with `isPlatformBrowser`.
