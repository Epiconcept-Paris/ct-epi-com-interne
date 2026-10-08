# Decision Records (ADR)

Index des décisions structurantes du dépôt. Une décision = un fichier
`D-NNN-<kebab-titre>.md`.

Guide méthodologique (quand ouvrir un ADR, workflows, anti-patterns) :
[adr-guide.md](adr-guide.md).

## Conventions

- **Filename** : `D-NNN-<kebab-titre>.md`. Le numéro `D-NNN` est
  l'identifiant primaire, invariant à perpétuité — jamais de
  renumérotation, les gaps sont acceptés.
- **Frontmatter YAML obligatoire** : `id`, `title`, `status`, `type`,
  `date`, `supersedes`, `amended_by`, `sources`, `patterns`
  (+ `verdict` pour les closures, `amends` pour les amendements,
  `phase` optionnel).
- **3 templates** : `_template_cadrage.md` (axes structurés),
  `_template_closure.md` (verdict empirique),
  `_template_amendement.md` (modification ciblée d'un ADR antérieur).
- **Statuts** : `draft` | `accepted` | `amended` | `superseded` |
  `deprecated`.
- **Types** : `cadrage` | `closure` | `amendement`.
- **Discipline** : un ADR = un commit, cette table mise à jour au même
  commit. Un amendement met à jour le frontmatter de l'ADR amendé
  (`amended_by`, `status`) au même commit, sans toucher son corps.

### Adaptations locales

- **Emplacement** : `docs/decisions/`, le chemin recommandé par le
  guide — aucune adaptation, depuis
  [D-013](D-013-amendement-d002-alignement-gabarit-de-depot.md) qui
  amende l'axe 2 de [D-002](D-002-convention-adr-et-tracage-retroactif.md)
  et aligne le dépôt sur le gabarit `ct-skill-template`.
- **Anciens chemins dans les corps figés** : `D-001` à `D-012` ont été
  écrits quand les ADR vivaient sous `documentation/adr/` et la
  documentation de référence sous `documentation/`. Leurs mentions de
  ces chemins se lisent `docs/decisions/` et `docs/` ; elles ne sont pas
  des erreurs et ne se corrigent pas (corps d'ADR publiés).
- **Périmètre du kit** : version minimale — guide, templates et index.
  Ni `adr_new.py`, ni `adr_check.py`, ni CI `adr-check`, ni schema JSON.
  Le guide y fait référence : ces mentions décrivent le kit, pas ce
  dépôt. Activables plus tard par simple copie.
- **`adr-guide.md` est une copie verbatim** du kit : ne pas le retoucher
  (cf. [D-002](D-002-convention-adr-et-tracage-retroactif.md),
  condition 2).
- **Antériorité** : `D-003` à `D-011` tracent des décisions prises avant
  l'existence du dépôt. Leur `date` est celle du traçage, leur
  déclencheur cite l'artefact (fichier:ligne) et non une citation de
  l'opérateur, et leurs minutes d'origine sont déclarées indisponibles
  plutôt que reconstituées. `D-001` et `D-002` échappent à ce régime
  (citation authentique de la session du 2026-09-09), et
  [D-012](D-012-environnement-cible-claude-ai.md) est un cas
  intermédiaire déclaré comme tel : citation authentique, mais posée sur
  une autre skill et appliquée ici **par analogie**, à confirmer.
- **Identifiants locaux au dépôt** : un `D-NNN` cité sans nom de dépôt
  désigne celui d'ici. Les dépôts de skills voisins (`ct-epi-visual`,
  `ct-fwk-specs-fonct`, `ct-ssi-tableau-de-bord`) ont leur propre série
  `D-NNN` ; toute référence croisée se qualifie
  (« `ct-fwk-specs-fonct` D-013 ») — cf.
  [D-001](D-001-mise-sous-depot-de-la-skill.md), axe 1, et
  [D-002](D-002-convention-adr-et-tracage-retroactif.md), axe 3.
- **`CHANGELOG.md`** (racine) versionne le **gabarit email**, pas les
  décisions : périmètres disjoints, aucun ADR n'y est listé.

## Index

Triées par ID décroissant (le plus récent en haut).

| ID | Titre | Status | Type | Date |
|---|---|---|---|---|
| [D-013](D-013-amendement-d002-alignement-gabarit-de-depot.md) | Amendement D-002 — ADR sous docs/decisions/, dépôt aligné sur le gabarit commun | accepted | amendement | 2026-10-08 |
| [D-012](D-012-environnement-cible-claude-ai.md) | Claude AI comme environnement cible, chemins et outils assumés | accepted | cadrage | 2026-09-09 |
| [D-011](D-011-bilingue-fr-en-par-adaptation.md) | Bilingue FR/EN dans un seul email, par adaptation et non par traduction | accepted | cadrage | 2026-09-09 |
| [D-010](D-010-conventions-editoriales-externalisees.md) | Conventions éditoriales externalisées, tirées de communications réelles | accepted | cadrage | 2026-09-09 |
| [D-009](D-009-turquoise-reserve-aux-bandeaux.md) | Turquoise réservé aux deux bandeaux, logo officiel jamais redessiné | accepted | cadrage | 2026-09-09 |
| [D-008](D-008-contraintes-gmail-comme-cadre-technique.md) | Les contraintes Gmail comme cadre technique du markup | accepted | cadrage | 2026-09-09 |
| [D-007](D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md) | Assemblage par le shell : les blobs base64 ne passent jamais par le contexte | accepted | cadrage | 2026-09-09 |
| [D-006](D-006-interrogation-prealable-et-validation-du-brouillon.md) | Interrogation préalable puis validation du brouillon texte : deux points d'arrêt | accepted | cadrage | 2026-09-09 |
| [D-005](D-005-gabarit-a-placeholders-et-snippets.md) | Gabarit à placeholders et snippets externalisés, structure verticale imposée | accepted | cadrage | 2026-09-09 |
| [D-004](D-004-html-autonome-cible-gmail.md) | Un HTML autonome ciblant Gmail, sans ESP ni dépendance externe | accepted | cadrage | 2026-09-09 |
| [D-003](D-003-declenchement-opt-in-strict.md) | Déclenchement opt-in strict, avec question préalable | accepted | cadrage | 2026-09-09 |
| [D-002](D-002-convention-adr-et-tracage-retroactif.md) | Adoption de la convention ADR et traçage rétroactif du gabarit | amended | cadrage | 2026-09-09 |
| [D-001](D-001-mise-sous-depot-de-la-skill.md) | Mise sous dépôt de la skill epi-com-interne, dans son propre dépôt | accepted | cadrage | 2026-09-09 |

## Patterns inscrits

Handles posés par ces ADR, citables et greppables.

| Pattern | ADR |
|---|---|
| `adaptation-plutot-que-traduction-litterale` | D-011 |
| `anteriorite-ancree-sur-artefact` | D-002 |
| `assemblage-hors-modele-fragments-seuls-generes` | D-007 |
| `asset-officiel-jamais-reproduit` | D-009 |
| `budget-mesurable-plutot-que-consigne-de-sobriete` | D-008 |
| `charte-consommee-source-de-verite-ailleurs` | D-009 |
| `cible-de-rendu-unique-assumee` | D-004 |
| `contrainte-documentee-avec-sa-cause` | D-008 |
| `convention-tiree-du-corpus-observe` | D-010 |
| `couleur-affectee-a-une-zone-unique` | D-009 |
| `deux-langues-un-seul-envoi` | D-011 |
| `distribution-par-release-taggee` | D-001 |
| `donnee-incompressible-jamais-en-contexte` | D-007 |
| `environnement-cible-unique-assume` | D-012 |
| `gabarit-externalise-forme-hors-regles` | D-005 |
| `interdiction-enoncee-avec-son-chiffre` | D-007 |
| `interdits-nommes-plutot-que-ton-decrit` | D-010 |
| `interrogation-prealable-groupee` | D-006 |
| `livrable-autonome-doc-hors-livrable` | D-001 |
| `livrable-self-contained-zero-dependance` | D-004 |
| `opt-in-strict-avec-question-prealable` | D-003 |
| `placeholder-comme-contrat-d-assemblage` | D-005 |
| `structure-alignee-sur-le-gabarit-commun` | D-013 |
| `un-depot-par-skill` | D-001 |
| `validation-au-stade-le-moins-couteux` | D-006 |
| `voix-externalisee-hors-regles` | D-010 |
