# Skills Optimus UI (ex-PrimeNG)

Pack de 3 skills qui apprennent à Claude (ou à tout agent compatible « Agent Skills ») à utiliser **Optimus UI** (`@openng/optimus-ui`), la continuation communautaire MIT de PrimeNG v21 pour Angular. Contenu vérifié sur la documentation officielle le 2026-10-12 (Optimus UI 2.0.2 / 1.0.2).

## Contenu

```
skills/
├── optimus-ui/                  # Usage au quotidien (déclenché en premier)
│   ├── SKILL.md                 #   règles d'or, workflow, setup, gabarit de composant
│   └── references/
│       ├── setup-and-config.md  #   install, provideOptimus, locale FR, z-index, CSP, icônes
│       ├── components-catalog.md#   80+ composants, "quel composant pour quoi", anciens noms PrimeNG
│       ├── component-patterns.md#   formulaires, table, overlays + services, pièges fréquents
│       └── live-docs.md         #   comment lire llms.txt / .md pour l'API exacte
├── optimus-ui-theming/          # Apparence
│   ├── SKILL.md                 #   échelle de décision, tokens, piège colorScheme, dark mode, [dt]
│   └── references/ tokens-recipes.md · tailwind.md · passthrough-and-unstyled.md
└── optimus-ui-migration/        # Migration & montées de version
    ├── SKILL.md                 #   arbre de décision A/B/C/D, schematic, vérifications
    └── references/ primeng-mapping.md
dist/                            # Les 3 skills empaquetés (.skill) pour claude.ai
CLAUDE.snippet.md                # Bloc à coller dans le CLAUDE.md / AGENTS.md du projet
evals/evals.json                 # 6 prompts de test réalistes pour valider les skills
```

## Pourquoi 3 skills et pas 1 ?

Chaque skill n'expose que son `name` + `description` en permanence ; le corps ne se charge que quand il sert. Séparer usage / apparence / migration garde le contexte léger et le déclenchement précis. Le skill principal route vers les deux autres et vers des fichiers `references/` chargés à la demande (progressive disclosure). Les skills ne recopient pas toute l'API des 80+ composants (elle change et serait obsolète) : ils donnent les règles, les pièges et **la méthode pour lire la doc officielle à jour** (`llms.txt`, pages `.md`, fichiers `.d.ts`).

## Installation

**Claude Code / projet versionné (recommandé)**
1. Copier `skills/*` dans `.claude/skills/` à la racine du projet (ou `~/.claude/skills/` pour tous vos projets).
2. Coller `CLAUDE.snippet.md` dans le `CLAUDE.md` du projet et renseigner la version épinglée (`@1` ou `@2`).

**claude.ai**
1. Paramètres → Capabilities → Skills → Upload.
2. Importer chacun des fichiers de `dist/` (`optimus-ui.skill`, `optimus-ui-theming.skill`, `optimus-ui-migration.skill`).

**Autres agents** : les dossiers suivent le format SKILL.md (frontmatter `name` + `description`). Sinon, concaténez `CLAUDE.snippet.md` + `optimus-ui/SKILL.md` dans le fichier d'instructions de votre agent.

## Points clés à retenir

- Optimus UI v1 ↔ Angular 21 ; v2 ↔ Angular 22. Le tag npm `latest` pointe sur la v2 : **sur Angular 21, installez `@openng/optimus-ui@1`**.
- Migration depuis PrimeNG v21 : `npm i @openng/optimus-ui@1` puis `ng generate @openng/optimus-ui@1:migrate-from-primeng` (pas `ng add`).
- Sélecteurs `p-*`, icônes `pi pi-*`, variables `--p-*` inchangés.
- Support communautaire uniquement (pas de SLA) ; Theme Designer / templates / blocks PrimeTek non inclus ; PrimeFlex → Tailwind.

## Tester les skills

`evals/evals.json` contient 6 prompts (formulaire, migration, thème + Tailwind, piège `colorScheme`, erreur de peer deps, grosse table). Lancez-les avec et sans les skills et comparez : sans skill, un modèle a tendance à importer `primeng`, utiliser `p-dropdown`, oublier `MessageService` ou installer la v2 sur Angular 21.

## Maintenance

Les faits datent du 2026-10-12. Re-vérifier à chaque nouvelle majeure d'Angular/Optimus : table de compatibilité (`/migration/update`), notes de version GitHub, et la page `llms.txt`. Points marqués *(verify)* dans les références = à confirmer sur la doc du composant avant usage.
