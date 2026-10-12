---
name: optimus-ui
description: Use for ANY work involving Optimus UI (@openng/optimus-ui), the MIT-licensed community continuation of PrimeNG v21 for Angular (formerly PrimeNG). Trigger on - optimus-ui, @openng/*, provideOptimus, p-button / p-table / p-select / p-dialog / p-datepicker / p-toast templates, pi pi-* icons, "PrimeNG" or "primeng" in an Angular project, choosing or configuring any Angular UI component (forms, data tables, dialogs, toasts, menus, tabs, overlays), `ng add @openng/optimus-ui`, version or Angular compatibility questions. Use it even when the user only says "PrimeNG", because PrimeNG packages must no longer be installed or imported. Also use for French requests (composant Angular, tableau de données, formulaire, boîte de dialogue, notification, bibliothèque UI). For theming/dark mode/Tailwind/pass-through use optimus-ui-theming; for migrating from PrimeNG or upgrading Optimus versions use optimus-ui-migration.
---

# Optimus UI — core usage

Optimus UI is a community-maintained, MIT-licensed suite of 80+ accessible Angular components. It is a rebranded fork of **PrimeNG v21** (the last MIT version), maintained by OpenNG. PrimeTek archived PrimeNG in June 2026 and moved v22+ to a commercial license; Optimus UI is not affiliated with PrimeTek. Selectors (`p-*`), icon classes (`pi pi-*`) and CSS variables (`--p-*`) are unchanged from PrimeNG, so most PrimeNG knowledge transfers; only package names, a few identifiers and the version scheme differ.

Facts below were verified against the official docs on 2026-10-12 (Optimus UI 2.0.2 / 1.0.2). Reply in the language the user writes in.

## Golden rules

1. **Never install or import `primeng`, `primeicons` or `@primeuix/*`.** Use `@openng/optimus-ui`, `@openng/icons`, `@openng/optimus-ui-themes`, etc. Mixing both ships two copies of every component and two theme runtimes. If you see a `primeng` import in generated or pasted code, translate it (see `optimus-ui-migration`).
2. **Match the major to Angular.** v1 ↔ Angular 21, v2 ↔ Angular 22 (majors follow Angular's). Read `@angular/core` in `package.json` first. The npm `latest` tag points at the *current* major, so on Angular 21 install `@openng/optimus-ui@1`, otherwise peer-dependency errors follow.
3. **Import each component from its own subpath**, standalone, in the component's `imports: []` — e.g. `import { Button } from '@openng/optimus-ui/button'`. Never import from the package root; subpaths keep bundles small. The subpath equals the old PrimeNG one (`primeng/table` → `@openng/optimus-ui/table`). The `XxxModule` form (e.g. `ButtonModule`) also exists; prefer the standalone class when the component's docs show it.
4. **Style through design tokens, not CSS overrides.** Use a preset (`definePreset`) or the scoped `[dt]` input. `::ng-deep` and `!important` are last resorts. Details in `optimus-ui-theming`.
5. **Do not guess APIs.** Props, emits and templates differ subtly between components and between Optimus majors, and Optimus v2 may diverge from PrimeNG docs and from memory. When you are not certain of an input/output name, read the component's live doc (see `references/live-docs.md`) or the installed `.d.ts` files before writing code. Never copy a snippet from primeng.org without applying the package mapping.
6. **Use the current component names.** Legacy PrimeNG names are not in the catalog: Dropdown→Select, Calendar→DatePicker, InputSwitch→ToggleSwitch, OverlayPanel→Popover, Sidebar→Drawer, TabView→Tabs, InputTextarea→Textarea, Steps→Stepper (full table in `references/components-catalog.md`).
7. **Write modern Angular**: standalone components, `inject()`, signals, `@if`/`@for` control flow. Optimus docs examples already use signals (`loading = signal(false)`).
8. **Keep accessibility intact.** The library targets WCAG 2.1 AA. Give icon-only buttons an `aria-label`, keep labels on inputs, never remove the focus ring (change it via the `focusRing` token), and localize ARIA strings through the `aria.*` locale keys.
9. **Know the limits.** Support is community-only (GitHub Issues/Discussions, Discord; no SLA). PrimeTek's Theme Designer, templates and blocks are commercial and are not part of Optimus. PrimeFlex is unmaintained here — use Tailwind CSS (`@openng/optimus-ui-tailwindcss`).

## Workflow for a UI task

1. **Detect context** (read, don't ask): `package.json` (Optimus or PrimeNG? Angular major? Tailwind v3/v4?), `app.config.ts` (`provideOptimus`, preset, `darkModeSelector`, `cssLayer`), `angular.json` styles (icons stylesheet).
2. **Pick components** with `references/components-catalog.md`. Prefer a purpose-built component over composing primitives (e.g. `p-select` rather than a custom dropdown, `p-table` rather than a hand-made grid).
3. **Check unfamiliar APIs** in the live docs (`references/live-docs.md`) — fetch only the pages you need.
4. **Apply the patterns** in `references/component-patterns.md` (forms, tables, overlays, required services, messages, performance) and the template below.
5. **Verify**: `ng build` (or the project's build/test command), then `grep -riEn "primeng|primeicons|@primeuix" src/ package.json` must return nothing for a pure Optimus project.

## Minimal setup (new project)

```shell
ng add @openng/optimus-ui                                  # asks for a preset: Aura | Lara | Material | Nora
ng add @openng/optimus-ui --theme=Aura --project=admin     # non-interactive / multi-project workspace
```

`ng add` only sets up *new* projects: if it finds a `primeng` dependency it changes nothing and fails — use the migration schematic instead. Manual path (see `references/setup-and-config.md` for icons, CSP, locale):

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideOptimus } from '@openng/optimus-ui/config';
import Aura from '@openng/optimus-ui-themes/aura';

export const appConfig: ApplicationConfig = {
  providers: [provideOptimus({ theme: { preset: Aura } })]
};
```

## Component template (copy this shape)

```typescript
import { Component, signal } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { Button } from '@openng/optimus-ui/button';
import { Select } from '@openng/optimus-ui/select';

@Component({
  selector: 'app-city-picker',
  imports: [FormsModule, Button, Select],
  template: `
    <p-select [options]="cities" [(ngModel)]="city" optionLabel="name" placeholder="Choose a city" />
    <p-button label="Save" icon="pi pi-check" [loading]="saving()" (onClick)="save()" />
  `
})
export class CityPicker {
  cities = [{ name: 'Paris' }, { name: 'Lyon' }];
  city: { name: string } | null = null;
  saving = signal(false);
  save() { /* ... */ }
}
```

Note `(onClick)` — not `(click)` — on `<p-button>`; `(click)` is for native `<button pButton>` elements.

## Quick routing

| Need | Go to |
|---|---|
| Choose a component, form/table/overlay patterns, services | `references/component-patterns.md`, `references/components-catalog.md` |
| Install, provider options, locale (French), CSP, z-index, icons | `references/setup-and-config.md` |
| Exact props/emits/templates/tokens of one component | `references/live-docs.md` |
| Colors, dark mode, tokens, Tailwind, pass-through, unstyled | skill `optimus-ui-theming` |
| From PrimeNG, upgrade v1→v2, `ng update` | skill `optimus-ui-migration` |
