# Live documentation lookup

This skill gives you rules, patterns and pitfalls. For the **exact** API of a component (inputs, outputs, templates, pass-through sections, design tokens) use the official LLM-optimized docs instead of memory. Optimus UI v2 can diverge from PrimeNG docs, so memory of PrimeNG is only a starting point.

## Endpoints

| What | URL |
|---|---|
| Index of all pages with one-line descriptions | `https://optimus.openng.org/llms/llms.txt` |
| Entire docs in one file (very large — grep it, never load it whole) | `https://optimus.openng.org/llms/llms-full.txt` |
| One component as Markdown | `https://optimus.openng.org/llms/components/<slug>.md` (e.g. `button.md`, `table.md`, `select.md`) |
| Human docs (current major, Angular 22) | `https://optimus.openng.org/<slug>` |
| Human docs for **v1 / Angular 21** | `https://v1.optimus.openng.org/<slug>` |
| Source | `https://github.com/openng-org/optimus-ui` (presets in `packages/optimus-ui-themes/src/presets`) |
| Questions / bugs | GitHub Discussions / Issues; Discord; "Ask AI" button on the docs site |

`<slug>` is the page name from `components-catalog.md` (note: `organizationchart`, `fileupload`, `inputotp` differ from their display names). The `.md` twin pattern was verified on `button.md`; if another slug 404s, fetch the HTML page instead.

If the project is on Angular 21 (Optimus v1), prefer the `v1.` docs host so the API matches the installed version.

## What a component `.md` contains

1. Demo sections — complete, copy-ready Angular snippets (imports + template + class).
2. `## <Component>` → **Props** table (name, type, default, description; includes `dt`, `unstyled`, `pt`, `ptOptions`), **Emits**, **Templates** (named `ng-template`s).
3. **Pass Through Options** — the section names usable in `[pt]` (note `pc*` entries for nested components).
4. **Theming** — CSS class names and the full **Design Tokens** table (token → `--p-…` variable). Use these names in `definePreset` / `[dt]`.

Read the demos for usage and the tables for exact names. Fetch only the components you need.

## Offline / ground truth

The installed package is authoritative for the installed version: inspect the type declarations under `node_modules/@openng/optimus-ui/<slug>/` (or grep `.d.ts` files) to confirm input/output names and generics. Use `grep -rn "selector: 'p-select'" node_modules/@openng/optimus-ui` style searches to find a component's selector and class.

## Etiquette

- Do not paste long doc passages back to the user; summarize and show the minimal working snippet.
- If the docs and your memory disagree, trust the docs and mention the difference.
- If something cannot be found (missing feature, unclear behavior), say so and suggest a GitHub Discussion rather than inventing an API.
