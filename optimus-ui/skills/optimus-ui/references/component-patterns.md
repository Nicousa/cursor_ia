# Component patterns and pitfalls

Contents: forms · tables · overlays and services · navigation/tabs · feedback · performance · common mistakes

Everything here is the stable PrimeNG-v21 lineage that Optimus UI v1 mirrors. Input/output names marked *(verify)* should be confirmed against the component's live doc before use (see `live-docs.md`), especially on Optimus v2.

## Forms

- Components implement Angular's `ControlValueAccessor`: use `[(ngModel)]` with `FormsModule`, or `formControlName`/`[formControl]` with `ReactiveFormsModule`. Forgetting the forms module is the #1 cause of "can't bind to ngModel".
- Text input is a **directive** on a native element: `<input pInputText [(ngModel)]="name" />` (import `InputText`). Textareas use `pTextarea` *(verify)* / the Textarea component.
- Numbers → `p-inputnumber` (supports currency, min/max, step, locale). Patterns → `p-inputmask`. `pKeyFilter` only restricts keystrokes; it is not validation.
- Selection → `p-select` (`optionLabel`, `optionValue`, `[filter]`, `[showClear]`), `p-multiselect`, `p-autocomplete` (`(completeMethod)` for suggestions).
- Dates → `p-datepicker`; format uses PrimeNG tokens (`dateFormat="dd/mm/yy"`). Set locale keys globally (see `setup-and-config.md`).
- Labels: `p-floatlabel` / `p-iftalabel` wrap input + `<label>`; `p-iconfield` + `p-inputicon` put an icon inside an input; `p-inputgroup` attaches add-ons/buttons.
- Full-width controls: wrap in `<p-fluid>` or set `fluid` on components that have it (Button does). Global look: `inputVariant: 'filled'` in `provideOptimus`.
- Validation messages: show inline errors with `<p-message severity="error">…</p-message>` under the control, driven by the form control's `touched && invalid`.
- Always pair controls with a visible `<label for>` (or `aria-label`/`ariaLabelledBy`) — accessibility depends on it.

```html
<form [formGroup]="form" (ngSubmit)="save()">
  <p-floatlabel>
    <input pInputText id="email" formControlName="email" />
    <label for="email">Email</label>
  </p-floatlabel>
  @if (form.controls.email.touched && form.controls.email.invalid) {
    <p-message severity="error">Invalid email</p-message>
  }
  <p-button type="submit" label="Save" [disabled]="form.invalid" />
</form>
```

## Tables (`p-table`)

```html
<p-table [value]="rows()" dataKey="id" [paginator]="true" [rows]="20" [rowsPerPageOptions]="[10,20,50]"
         [(selection)]="selected" [globalFilterFields]="['name','status']" #dt>
  <ng-template #header>
    <tr><th pSortableColumn="name">Name <p-sortIcon field="name" /></th><th>Status</th></tr>
  </ng-template>
  <ng-template #body let-row>
    <tr><td>{{ row.name }}</td><td><p-tag [value]="row.status" /></td></tr>
  </ng-template>
  <ng-template #emptymessage><tr><td colspan="2">No rows</td></tr></ng-template>
</p-table>
```

- Templates are `<ng-template #header|#body|#footer|#emptymessage|…>` (the older `pTemplate="header"` also works). Always set `dataKey` for selection, row expansion and editing, and so Angular can track rows.
- Server-side data: `[lazy]="true"`, `(onLazyLoad)="load($event)"`, `[totalRecords]`, `[loading]` — the event carries `first`, `rows`, `sortField/Order`, `filters`. Do not load tens of thousands of rows client-side.
- Large client-side sets: `[virtualScroll]="true"` with `scrollHeight` and `[virtualScrollItemSize]`, or paginate.
- Global search: call `dt.filterGlobal(value, 'contains')` from an input; per-column filters use `FilterMatchMode` (defaults configurable globally via `OptimusConfig.filterMatchModeOptions`).
- Export, grouping, frozen columns, row/cell editing, column resize/reorder exist — read the Table doc for the exact inputs *(verify)*.
- Hierarchies → `p-treetable`; card/grid listings → `p-dataview`.

## Overlays and the services they require

| Component | Needs | Import services from |
|---|---|---|
| `p-toast` | `MessageService` provided + one `<p-toast />` in the shell | `@openng/optimus-ui/api` |
| `p-confirmdialog` / `p-confirmpopup` | `ConfirmationService` | `@openng/optimus-ui/api` |
| DynamicDialog | `DialogService` (opens any component as dialog content, returns a `DynamicDialogRef`) | `@openng/optimus-ui/dynamicdialog` |

- Provide the service where it is shared: `providers: [MessageService]` in `app.config.ts` (app-wide) — a different instance per component silently hides toasts. Error "No provider for MessageService/ConfirmationService" = missing provider.
- Put **one** `<p-toast />` and one `<p-confirmdialog />` in the root component, not in every feature; use `key`/`position` only when you need several channels *(verify)*.
- `p-dialog`: `[(visible)]="open"`, `[modal]="true"`, `header`, `[style]`; footer via template. Prefer `appendTo="body"` (or global `overlayAppendTo: 'body'`) when the trigger lives inside `overflow:hidden` containers.
- Side panels use `p-drawer` (was Sidebar); anchored panels use `p-popover` (was OverlayPanel) and are toggled with `popover.toggle($event)` from a template ref.
- Overlay layering is automatic; only touch `zIndex` config for fixed headers.

```typescript
private messages = inject(MessageService);
this.messages.add({ severity: 'success', summary: 'Saved', detail: 'Item created', life: 3000 });
```

## Navigation and tabs

- Menus take a `MenuItem[]` model (`label`, `icon`, `routerLink`, `command`, `items`, `separator`, `disabled`) via `[model]`. Same model type for `menubar`, `menu`, `tieredmenu`, `megamenu`, `panelmenu`, `contextmenu`, `breadcrumb` (`[model]` + `[home]`).
- Tabs (replaces TabView/TabMenu): `<p-tabs [(value)]="tab"><p-tablist><p-tab value="a">A</p-tab></p-tablist><p-tabpanels><p-tabpanel value="a">…</p-tabpanel></p-tabpanels></p-tabs>`. For route-driven tabs use `routerLink` on `p-tab` *(verify)*.
- Wizards: `p-stepper` *(verify structure in docs)*.

## Feedback

`p-toast` for transient results, `p-message` for persistent inline state, `p-progressbar` / `p-progressspinner` for async work, `p-skeleton` as loading placeholders (better than spinners for lists/tables), `[loading]` input on `p-button`/`p-table`, `p-blockui` to block a region.

## Performance

- Import only what you use, from subpaths (tree-shaking relies on it).
- Tables/lists: `lazy` or virtual scroll for large data; stable `dataKey`; avoid heavy functions in templates (use `computed`).
- `ripple` is opt-in — leave it off unless the design needs it.
- Enable `cssLayer` only when you need predictable override order (e.g. Tailwind); it is off by default.
- Lazy-route heavy components (Editor, Chart, FileUpload); Chart.js and Quill are external dependencies of Chart/Editor.

## Common mistakes checklist

1. `npm i @openng/optimus-ui` on Angular 21 → installs v2. Use `@1`.
2. Legacy components (`p-dropdown`, `p-calendar`, `TabView`…) → see the catalog mapping.
3. `(click)` on `<p-button>` → use `(onClick)`.
4. Missing `FormsModule`/`ReactiveFormsModule`, or missing `MessageService`/`ConfirmationService` provider.
5. Icons invisible → `@openng/icons` not installed or `openng-icons.css` not imported.
6. Theme override ignored → token defined under `colorScheme` in the preset (see theming skill).
7. Tailwind utilities lose to component CSS → enable `cssLayer` with the documented order, not `!important` everywhere.
8. Dark mode toggled by a class, but `darkModeSelector` still `'system'` (or not aligned with Tailwind's dark variant).
9. Overlay clipped inside a container → `appendTo="body"` / `overlayAppendTo: 'body'`.
10. Importing from `@openng/optimus-ui` root or from `primeng/*` out of habit.
