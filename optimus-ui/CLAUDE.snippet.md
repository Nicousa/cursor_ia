## UI library: Optimus UI (@openng/optimus-ui)

This project uses **Optimus UI**, the MIT community continuation of PrimeNG v21. It is NOT PrimeNG.

- Never install or import `primeng`, `primeicons` or `@primeuix/*`. Use `@openng/optimus-ui`, `@openng/optimus-ui-themes`, `@openng/icons`.
- Version ↔ Angular: Optimus v1 = Angular 21, v2 = Angular 22. <!-- EDIT: pinned version for this project, e.g. "@openng/optimus-ui@1" -->
- Import components from subpaths: `import { Button } from '@openng/optimus-ui/button'`. Selectors stay `p-*`, icons `pi pi-*`.
- Style via design tokens (`definePreset`, `[dt]`) and pass-through (`[pt]`), not `::ng-deep`.
- Before writing code that touches an Optimus component, use the skills in `.claude/skills/`: `optimus-ui` (usage), `optimus-ui-theming` (look & Tailwind), `optimus-ui-migration` (PrimeNG/upgrade).
- If unsure about an input/output name, read `https://optimus.openng.org/llms/components/<slug>.md` (v1: `https://v1.optimus.openng.org/<slug>`) or the installed `.d.ts` — do not guess from PrimeNG memory.
- After changes: run the build and `grep -riEn "primeng|primeicons|@primeuix" src/ package.json` (must be empty).
