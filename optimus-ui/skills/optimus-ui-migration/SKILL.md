---
name: optimus-ui-migration
description: Use when moving a project to Optimus UI (@openng/optimus-ui) or between its versions - migrating FROM PrimeNG (primeng, primeicons, @primeuix/themes, providePrimeNG, primeng/* imports), the migrate-from-primeng schematic, handling leftover references, PrimeNG v20 or older that must reach v21 first, PrimeNG v22 (commercial) questions, upgrading Optimus v1 (Angular 21) to v2 (Angular 22) with ng update, version pinning, licensing/trademark rename questions. Trigger on "migrer depuis PrimeNG", "remplacer primeng", "PrimeNG is no longer open source", "upgrade Angular 22 with Optimus", "ng update @openng/optimus-ui", or any code that still imports primeng.
---

# Optimus UI — migration and upgrades

Optimus UI v1 is PrimeNG v21 rebranded and API-compatible, so moving from PrimeNG v21 is a **rename, not a rewrite**: selectors keep `p-`, icon classes keep `pi-`, templates are untouched. From v1 onward Optimus evolves independently and will diverge from PrimeNG, so v1 is the smoothest moment to switch. Verified against the official migration and update guides (2026-10-12).

## Step 0 — identify the starting point

Read `package.json` and answer: which of `primeng` / `@openng/optimus-ui` is installed, at which version, and which Angular major?

| Situation | Path |
|---|---|
| PrimeNG **v21** on Angular 21 | **A.** Migrate with the schematic → Optimus v1 |
| PrimeNG **≤ v20** | **B.** `ng update primeng@21` first (follow PrimeNG's own v21 migration guide), then A. The schematic refuses older versions. |
| PrimeNG **v22+** (commercial) | Not a supported source: Optimus v1 mirrors v21's API. Downgrade to v21 (check license/terms yourself) or migrate component by component after reviewing diffs; flag the risk to the user. |
| Optimus **v1** (Angular 21) wanting Angular 22 | **C.** Upgrade Angular, then `ng update @openng/optimus-ui@2` |
| Optimus, same Angular major | **D.** `ng update @openng/optimus-ui` (minor/patch are drop-in) |
| New project | Not a migration — see `optimus-ui` skill (`ng add`) |

## Path A — PrimeNG v21 → Optimus UI v1 (automated)

Work on a clean git tree so the changes can be reviewed in one diff.

```shell
npm install @openng/optimus-ui@1                              # pin @1 — the "latest" tag points at the current major (v2)
ng generate @openng/optimus-ui@1:migrate-from-primeng
# flags: --skip-install (don't run the package install)   --force (bypass the PrimeNG v21 check, e.g. non-standard dependency location)
```

What the schematic does: swaps the PrimeNG package family for the Optimus equivalents in every `package.json`; rewrites import specifiers and renamed identifiers (e.g. `providePrimeNG`) in `.ts`/`.mts`; updates asset references (PrimeIcons stylesheet) in stylesheets and `angular.json`/`project.json`; prints a **report of leftovers** (`primeng`, `primeicons`, `@primeuix` references it could not rewrite, with file and line); schedules the install. It is safe to re-run (e.g. after merging unmigrated branches). Files excluded by `.gitignore` are untouched. The leftover report is printed *before* the CLI's "created/updated files" list — scroll up to find it.

**Do not use `ng add @openng/optimus-ui` here**: it detects `primeng`, changes nothing and fails on purpose. (The CLI installs the package before the schematic runs, so the dependency stays in `package.json` — the migration needs it there.)

## Manual migration / finishing leftovers

Use the tables in `references/primeng-mapping.md` (packages, identifiers, assets, CSS layer, extra checks). Essentials:

```shell
npm uninstall primeng primeicons @primeuix/themes
npm install @openng/optimus-ui@1 @openng/optimus-ui-themes@1 @openng/icons
```

- `primeng/button` → `@openng/optimus-ui/button` (suffix kept).
- `providePrimeNG` → `provideOptimus` (from `@openng/optimus-ui/config`); `PrimeNG` service → `Optimus`; `PrimeNGConfigType` → `OptimusConfigType`; `PrimeIcons` → `OpenngIcons`.
- `@primeuix/themes/aura` → `@openng/optimus-ui-themes/aura`; `primeicons/primeicons.css` → `@openng/icons/openng-icons.css`.

## Verify (always)

```shell
ng build
grep -riEn "primeng|primeicons|@primeuix" src/ package.json angular.json *.json *.scss 2>/dev/null
```
Then run unit/e2e tests and eyeball a page with icons, a dialog, a table, dark mode. Also search `tailwind` configs, Dockerfiles, CI scripts and docs for old package names (the schematic only covers source/package/asset files).

## Path C — Optimus v1 → v2 (Angular 21 → 22)

```shell
ng update @angular/core@22 @angular/cli@22      # Angular first — see angular.dev/update-guide
ng update @openng/optimus-ui@2
```
`ng update` bumps every installed `@openng/optimus-ui-*` package together (they are a declared group, so mixed versions can't happen) and runs bundled code migrations. It expects a clean git tree. **Read the release notes/CHANGELOG of the target major for breaking changes before running it** — breaking changes are confined to majors and documented there. Run `ng update` with no arguments to see what is available.

## Path D — same-major updates

`ng update @openng/optimus-ui` — minor/patch releases stay within the same Angular major and are drop-in.

## After migrating — what changes for the team

- **Version scheme**: Optimus majors track Angular majors (v1↔21, v2↔22). Pin accordingly in CI/Docker and when installing.
- **Docs**: use `v1.optimus.openng.org` for v1, `optimus.openng.org` for the current major. PrimeNG docs stay a reference for concepts only, not for imports.
- **Ecosystem**: PrimeTek's Theme Designer, templates/blocks and a PrimeNG v22 license do not carry over (and are not needed — Optimus needs no license). Presets you wrote against PrimeNG v21 keep working once import paths are updated. PrimeFlex is unmaintained → move to Tailwind + `@openng/optimus-ui-tailwindcss` (`/guides/primeflex`).
- **Support**: community only (GitHub Issues/Discussions, Discord), no SLA. Mention this if the user's organization needs contractual support.
- Running PrimeNG and Optimus side by side is technically possible (different package names and CSS layer) but unsupported beyond a temporary state — two component copies and two theme runtimes.
