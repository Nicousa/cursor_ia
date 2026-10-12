# Component catalog (80+)

Import rule: `import { X } from '@openng/optimus-ui/<slug>'` (the slug is the lowercase page name below; it equals the former `primeng/<slug>` path). Each component's doc page states its exact import path and class names — confirm in the live docs if unsure. Docs: `https://optimus.openng.org/<slug>` and the Markdown twin `https://optimus.openng.org/llms/components/<slug>.md`.

Selectors keep the `p-` prefix (`<p-select>`, `<p-table>`); some are directives (`pInputText`, `pTooltip`, `pRipple`, `pButton`).

## Form
autocomplete · cascadeselect · checkbox · colorpicker · datepicker · editor (Quill-based rich text) · floatlabel · iconfield · iftalabel · inputgroup · inputmask · inputnumber · inputotp · inputtext · keyfilter · knob · listbox · multiselect · password · radiobutton · rating · select · selectbutton · slider · textarea · togglebutton · toggleswitch · treeselect

## Button
button (also `pButton` directive with `pButtonLabel`/`pButtonIcon`, and `p-buttongroup`) · speeddial · splitbutton

## Data
dataview · orderlist · organizationchart · paginator · picklist · table · timeline · tree · treetable · virtualscroller

## Panel
accordion · card · divider · fieldset · panel · scrollpanel · splitter · stepper · tabs · toolbar

## Overlay
confirmdialog · confirmpopup · dialog · drawer · dynamicdialog · popover · tooltip

## File
fileupload (drag & drop, multi-file, auto upload, progress, validation)

## Menu
breadcrumb · contextmenu · dock · menu · menubar · megamenu · panelmenu · tieredmenu

## Chart
chart (Chart.js 3.3.2+ — Chart.js is a separate dependency)

## Messages
message (inline) · toast (overlay; needs `MessageService`)

## Media
carousel · galleria · image · imagecompare

## Misc
animateonscroll · autofocus · avatar · badge · bind · blockui · chip · classnames · dragdrop (`pDraggable`/`pDroppable`) · fluid · focustrap · inplace · metergroup · progressbar · progressspinner · ripple · scrolltop · skeleton · styleclass · tag · terminal

## Utilities
filterservice · overlay (Overlay API) · plus `@openng/optimus-ui/api` (`MessageService`, `ConfirmationService`, `MenuItem`, `FilterMatchMode`, `OptimusConfig`, …) and `@openng/optimus-ui/config` (`provideOptimus`, `Optimus`)

## "Which component for…" (decision hints)

| Need | Use | Not |
|---|---|---|
| Pick one option from a list | `select` (filterable, grouped, virtual scroll) | custom dropdown; `listbox` is for always-visible lists |
| Pick many options | `multiselect`; `selectbutton` for ≤5 visible choices; `listbox [multiple]` | many checkboxes |
| Free text with suggestions | `autocomplete` | `select` with typing hack |
| Numbers, currency, steppers | `inputnumber` | `<input type="number">` |
| Pattern input (phone, date mask) | `inputmask` | regex in `(input)` |
| Block characters while typing | `keyfilter` (keystrokes only) | validation replacement |
| Dates / times / ranges | `datepicker` | native date input (inconsistent styling) |
| Boolean | `checkbox`, `toggleswitch`, `togglebutton` | |
| Tabular data, sorting, filtering, paging | `table`; `treetable` for hierarchies | hand-built `<table>` |
| Card/list/grid of records with paging | `dataview` | |
| Huge lists | `virtualscroller`, or `table` with `virtualScroll`/`lazy` | rendering all rows |
| Modal window | `dialog`; `dynamicdialog` when content is a component opened from code; `confirmdialog` for yes/no | |
| Side panel | `drawer` | |
| Anchored floating content | `popover`; `confirmpopup` for inline confirmations; `tooltip` for hints | |
| Notifications | `toast` (transient), `message` (inline, persistent) | |
| Navigation | `menubar` (top), `panelmenu` (side), `breadcrumb`, `tabs` (with `routerLink` for route tabs), `stepper` (wizard) | |
| Layout containers | `card`, `panel`, `fieldset`, `accordion`, `splitter`, `toolbar`, `divider` | |
| Make controls span full width | `fluid` wrapper or the `fluid` input (e.g. on `p-button`) | |

## Legacy PrimeNG names that no longer exist

Deprecated PrimeNG components were replaced by current ones; the legacy names are absent from the Optimus catalog. When a snippet or old code uses them, translate:

| Legacy | Use |
|---|---|
| Dropdown (`p-dropdown`) | Select (`p-select`) |
| Calendar (`p-calendar`) | DatePicker (`p-datepicker`) |
| InputSwitch | ToggleSwitch |
| OverlayPanel | Popover |
| Sidebar | Drawer |
| TabView / TabMenu | Tabs (`p-tabs`, `p-tablist`, `p-tab`, `p-tabpanels`, `p-tabpanel`) |
| Steps | Stepper |
| InputTextarea | Textarea |
| Messages (plural) | Message |

The mapping follows PrimeNG's own rename lineage; confirm the new component's inputs in its doc page — they are not always 1:1 (e.g. Tabs uses `value` plus separate list/panel components).
