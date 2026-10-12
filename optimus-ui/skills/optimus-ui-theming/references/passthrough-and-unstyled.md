# Pass Through (`pt`) and unstyled mode

Verified from the official Pass Through and Unstyled docs (Optimus UI 2.0.2).

## Pass Through

Every component has a `pt` input: an object whose keys are the component's internal DOM **sections**. A value can be:
- a **string** (or a function returning one) → shorthand for CSS classes;
- an **object** → arbitrary attributes: `class`, `style`, `aria-*`, `data-*`, event listeners;
- a **function** receiving a `PassThroughContext` → computed values / access to the component instance.

Section names per component are listed in its doc page under **Pass Through Options** (e.g. Button: `host`, `root`, `loadingIcon`, `icon`, `label`, `pcBadge`). Use it whenever the component API lacks a hook you need (extra attributes, per-part classes, listeners) instead of `::ng-deep`.

```html
<p-panel header="Title" [pt]="{
  header: { class: 'bg-primary text-primary-contrast', 'data-testid': 'panel-header', onclick: onHeaderClick },
  content: 'font-medium'
}">…</p-panel>
```

Typed form (nicer in TS): 
```typescript
import { Panel, PanelPassThrough } from '@openng/optimus-ui/panel';
pt: PanelPassThrough<Panel> = { header: { class: 'border border-primary' } };
```

- **`pc` prefix**: sections for *nested components* start with `pc` (Button's badge is `pcBadge`); they take that component's own pass-through shape.
- **Lifecycle hooks**: under `hooks`: `onBeforeInit, onInit, onChanges, onDoCheck, onAfterContentInit, onAfterContentChecked, onAfterViewInit, onAfterViewChecked, onDestroy`.
- **Global**: `provideOptimus({ pt: { panel: { header: { class: 'bg-primary' } } } })` applies to all instances; a component's own `pt` has higher precedence by default.

### `ptOptions` — merging local and global

`[ptOptions]="{ mergeSections: true, mergeProps: false }"` (these are the defaults).
- `mergeSections` (default `true`): include sections that exist only in the global config.
- `mergeProps` (default `false`): `false` = local props *replace* global props of the same section; `true` = classes/props are *merged*.

Example: global `header.class = 'bg-primary text-primary-contrast'`, local `header.class = 'border border-primary'`:
- `mergeProps: false` → `'border border-primary'` (global class dropped)
- `mergeProps: true`  → `'bg-primary text-primary-contrast border border-primary'`
- `mergeSections: false` drops global sections the component did not mention at all.

## Unstyled mode

"Unstyled" removes the design-token CSS variables and the rules that use them; behavior and accessibility remain. Use it only when you own the whole look (design system, Tailwind-first apps). Otherwise stay in styled mode and customize tokens.

```typescript
provideOptimus({ unstyled: true })                       // whole suite
```
```html
<p-button label="Unstyled" icon="pi pi-check" [unstyled]="true" />   <!-- one component, even in styled mode -->
```

Recommended pairing: **unstyled + Tailwind + `pt`**, centralised in global `pt` so styles live in one place:

```typescript
provideOptimus({
  unstyled: true,
  pt: { button: {
    root: 'bg-teal-500 hover:bg-teal-700 active:bg-teal-900 cursor-pointer py-2 px-4 rounded-full border-0 flex gap-2',
    label: 'text-white font-bold text-lg',
    icon: 'text-white text-xl'
  } }
})
```

`@openng/optimus-ui-tailwindcss` also provides state variants for Optimus components such as `p-outlined:` and `p-vertical:` (check its docs for the full list). Read a component's Pass Through section before styling it so you target the right sections (`root`, `label`, `icon`, …).

## RTL

A dedicated guide exists at `https://optimus.openng.org/guides/rtl` (right-to-left support); consult it for the required `dir` handling rather than guessing.
