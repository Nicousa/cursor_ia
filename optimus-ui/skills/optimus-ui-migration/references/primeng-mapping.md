# PrimeNG v21 → Optimus UI v1 mapping

Source: official migration guide (tables verbatim in substance). Items marked *(not in the official table)* are inferences — verify before relying on them.

## Packages

| PrimeNG | Optimus UI |
|---|---|
| `primeng` | `@openng/optimus-ui` |
| `primeicons` | `@openng/icons` |
| `@primeuix/themes` | `@openng/optimus-ui-themes` |
| `@primeuix/styled` | `@openng/optimus-ui-styled` |
| `@primeuix/styles` | `@openng/optimus-ui-styles` |
| `@primeuix/utils` | `@openng/optimus-ui-utils` |
| `@primeuix/motion` | `@openng/optimus-ui-motion` |
| `tailwindcss-primeui` *(not in the official table)* | `@openng/optimus-ui-tailwindcss` (import changes: `@import "@openng/optimus-ui-tailwindcss";`) |
| `@primeng/themes` (older, pre-v20 name) *(not in the table)* | `@openng/optimus-ui-themes` |

Subpaths keep their suffix: `primeng/table` → `@openng/optimus-ui/table`, `primeng/api` → `@openng/optimus-ui/api`, `primeng/config` → `@openng/optimus-ui/config`, `@primeuix/themes/aura` → `@openng/optimus-ui-themes/aura`.

## Identifiers

| PrimeNG | Optimus UI |
|---|---|
| `providePrimeNG` | `provideOptimus` |
| `PrimeNG` (config service) | `Optimus` |
| `PrimeNGConfigType` | `OptimusConfigType` |
| `PrimeIcons` | `OpenngIcons` (`PrimeIcons` still compiles via a deprecated alias) |
| `PrimeNGConfig` (from `primeng/api`) *(not in the official table; inferred)* | `OptimusConfig` from `@openng/optimus-ui/api` — the Configuration docs use it for `zIndex` and `filterMatchModeOptions`; confirm in your major |

## Assets and styles

| Where | Before | After |
|---|---|---|
| global stylesheet / `angular.json` `styles` | `primeicons/primeicons.css` | `@openng/icons/openng-icons.css` |
| SCSS `@import` | `@import "primeicons/primeicons.css";` | `@import "@openng/icons/openng-icons.css";` |
| CSS cascade layer name (if `cssLayer` used) *(FAQ confirms the layer was renamed)* | `primeng` | `optimus` — update `cssLayer.name`, any `order` string and every `@layer … primeng …` statement |

Unchanged: `p-*` selectors, `pi pi-*` classes, `--p-*` CSS variables (default prefix `p`), template syntax.

## Before / after

```typescript
// BEFORE
import { providePrimeNG } from 'primeng/config';
import Aura from '@primeuix/themes/aura';
providers: [providePrimeNG({ theme: { preset: Aura } })]
import { ButtonModule } from 'primeng/button';

// AFTER
import { provideOptimus } from '@openng/optimus-ui/config';
import Aura from '@openng/optimus-ui-themes/aura';
providers: [provideOptimus({ theme: { preset: Aura } })]
import { ButtonModule } from '@openng/optimus-ui/button';
```

## Extra places the schematic does not cover — grep them

- `tailwind.config.*` / CSS `@import`/`@plugin` for the Tailwind plugin
- `Dockerfile`, CI YAML, `.npmrc`/lockfile pins (pin `@openng/optimus-ui@1` on Angular 21)
- Documentation, README, ADRs, code-generation prompts or AI-assistant instructions that still say PrimeNG
- Custom presets written with `@primeuix/*` imports in non-TS files
- Storybook/test setup files outside the workspace root, monorepo packages with non-standard dependency declarations (use `--force` if the PrimeNG v21 check misfires)

## Known non-goals / differences to communicate

- No Theme Designer, templates or blocks (PrimeTek commercial) — presets are edited in code.
- PrimeFlex not maintained → Tailwind.
- No commercial support or SLA.
- Future Optimus majors diverge from PrimeNG: do not assume new PrimeNG v22+ features or docs apply.
