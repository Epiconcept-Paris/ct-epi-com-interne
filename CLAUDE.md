# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this
repository.

## Nature du dépôt

Ce dépôt n'est **pas une application** : c'est une **skill Claude** (`skill/`) qui produit une
communication interne Epiconcept sous forme d'email HTML autonome, prêt à coller dans Gmail. Le
livrable réutilisable est le dossier `skill/` ; `docs/` est la référence de maintenance (non
embarquée dans la skill, qui doit rester autonome).

Le dépôt contient du Markdown, deux fichiers HTML et trois PNG — aucun script. Aucun gestionnaire
de paquets, aucun build, aucun linter n'est configuré. `tests/tests_unitaires/` reste donc vide ;
`tests/empirical_tests/` accueille les tests de comportement à jouer à la main (voir § Tests).

Le CI (`.github/workflows/release.yml`) se déclenche uniquement sur publication d'une release
GitHub et zippe `skill/` pour l'upload dans Claude AI.

Le seul contrôle de rendu est manuel : ouvrir le `.html` produit dans un navigateur, puis le
coller dans Gmail — cf. la checklist de `docs/PREREQUIS_TECHNIQUES.md`.

Dépôts voisins, à ne pas confondre :

- **`ct-epi-visual`** — la charte graphique Epiconcept, dont ce dépôt est **consommateur**. Elle
  fait autorité sur la palette (`#4BCDDB`, `#246589`) et sur le logo « e » officiel : une
  divergence se corrige là-bas d'abord (cf. `docs/MAINTENANCE_GABARIT.md`).
- **`ong-linkedin-post`**, **`epi-visual`** — skills voisines par le sujet, pas par la forme :
  posts LinkedIn d'une part, documents bureautiques DOCX / PPTX / XLSX d'autre part. Elles ne
  produisent pas d'email ; ce dépôt ne produit ni post ni document.

## Architecture

### Trois fichiers, trois rôles disjoints

- `skill/SKILL.md` — le **workflow et les règles** : quand se déclencher, quoi demander, comment
  assembler, quelles couleurs, quelles contraintes Gmail.
- `skill/assets/template.html` — le **gabarit** : la structure verticale en cinq blocs, le
  conteneur 600px, les styles inline, les placeholders `{{…}}`.
- `skill/references/editorial-patterns.md` — le **guide éditorial** : ton, structure type,
  emoji-ancres, signature, lignes de sujet, formulations proscrites.

La frontière est stricte ([D-005](docs/decisions/D-005-gabarit-a-placeholders-et-snippets.md),
[D-010](docs/decisions/D-010-conventions-editoriales-externalisees.md)) : **la forme dans le
gabarit, la procédure dans les règles, la voix dans les références.** Une valeur de padding
recopiée dans `SKILL.md` divergera du template ; une consigne de ton glissée dans
`SKILL.md` doublera `editorial-patterns.md`.

Il n'y a pas de chargement sélectif : les fichiers sont petits et tous utiles à chaque usage.

### La règle la plus importante : les blobs base64 ne passent pas par le contexte

`skill/assets/banner-fallback-snippets.html` embarque le logo « e » en data URI
(~6 000 caractères). `SKILL.md:53` interdit de le réécrire, et impose l'assemblage par
le shell ([D-007](docs/decisions/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md)).

Concrètement, à l'exécution : Claude écrit **seulement** les fragments éditoriaux
(`content_fr.html`, `content_en.html`, `signature.html`), puis un script `sed` /
`python3` extrait les snippets, substitue les placeholders et écrit le fichier final. Jamais
d'appel d'écriture avec le HTML complet.

Cette interdiction vise **l'exécution de la skill, pas la maintenance du dépôt** : en tant que
Claude Code travaillant ici, ouvrir les snippets est légitime — mais filtrer les blobs avant de
les afficher (`sed 's/base64,[A-Za-z0-9+/=]\{30,\}/base64,<BLOB>/g'`) évite de gaspiller le
contexte pour rien.

> **Toute modification d'un placeholder ou d'un délimiteur de snippet doit être répercutée dans
> `SKILL.md` et dans l'en-tête de commentaires du template.** La documentation *est* l'interface
> d'assemblage — un `{{PLACEHOLDER}}` renommé sans mise à jour casse l'email silencieusement : le
> texte de substitution reste visible dans le livrable.

### Deux points de contrôle utilisateur, pas un

Le workflow impose **deux** arrêts ([D-006](docs/decisions/D-006-interrogation-prealable-et-validation-du-brouillon.md)) :

1. les cinq questions de cadrage **avant** de rédiger (`SKILL.md:26-36`) ;
2. le **brouillon texte** soumis à validation **avant** de générer le HTML (`SKILL.md:49`).

Ne pas fusionner les deux, ne pas sauter le second : « la mise en page coûte cher à refaire, le
texte est plus rapide à itérer ». Une correction de fond après assemblage impose de tout
réassembler.

### Le turquoise est un invariant de marque, pas un choix de style

`#4BCDDB` en **fond du bandeau haut uniquement** ; corps du mail blanc ; `#246589` réservé au
**texte** des titres ; footer 11px `#999999` dans la carte, sous la signature
([D-009](docs/decisions/D-009-turquoise-reserve-aux-bandeaux.md), amendé par [D-015](docs/decisions/D-015-amendement-d009-turquoise-reserve-au-bandeau-haut.md)). Pas de bandeau en bas, pas de dégradé
turquoise → bleu foncé, pas de bloc intermédiaire coloré, jamais de bleu foncé en fond.

Ces valeurs viennent de la charte portée par `ct-epi-visual` : c'est **elle** qui fait autorité,
ce dépôt en est consommateur (cf. `docs/MAINTENANCE_GABARIT.md`).

## Invariants à ne pas casser

- **Déclenchement opt-in strict ([D-003](docs/decisions/D-003-declenchement-opt-in-strict.md))** :
  la skill ne s'active jamais d'elle-même, même sur un email interne Epiconcept manifeste. La
  `description` de la frontmatter (`skill/SKILL.md:3`) est un contrat, pas une formulation à
  « rendre plus naturelle » — la reformuler réactive le déclenchement automatique. Toute
  modification passe par un ADR d'amendement.
- **Jamais de base64 en contexte ([D-007](docs/decisions/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md))** :
  assemblage en shell, fragments éditoriaux seuls écrits par Claude.
- **HTML autonome, cible Gmail ([D-004](docs/decisions/D-004-html-autonome-cible-gmail.md))** :
  un fichier, zéro dépendance externe, zéro ESP. Tables, styles inline, 600px, < 102 Ko.
- **Turquoise réservé au bandeau haut ([D-009](docs/decisions/D-009-turquoise-reserve-aux-bandeaux.md), [D-015](docs/decisions/D-015-amendement-d009-turquoise-reserve-au-bandeau-haut.md))**,
  et logo « e » = image officielle — jamais reproduit par formes dessinées, jamais en SVG inline
  (Gmail le strippe).
- **Deux points de contrôle utilisateur ([D-006](docs/decisions/D-006-interrogation-prealable-et-validation-du-brouillon.md))** :
  cadrage puis brouillon texte. Ne pas inventer le contenu manquant, le demander.
- **`skill/` autonome ([D-001](docs/decisions/D-001-mise-sous-depot-de-la-skill.md))** : la
  skill ne référence jamais `docs/` ni `tests/`. Un zip de `skill/` seul doit fonctionner.
- **Environnement cible = Claude AI ([D-012](docs/decisions/D-012-environnement-cible-claude-ai.md))** :
  les chemins `/mnt/skills/user/…`, `/home/claude/`, `/mnt/user-data/outputs/` et les outils
  `bash_tool` / `ask_user_input_v0` / `present_files` sont assumés, pas à « rendre portables ».
- **Aucun secret** dans la skill, et **aucun tracking** dans les emails produits : pas de pixel,
  pas de JS, pas de formulaire.
- **Langue** : documentation, commentaires et libellés en **français accentué**. Ne jamais omettre
  les accents (« résumé exécutif », pas « resume executif »). Exceptions : code, noms de
  fichiers/classes, termes techniques anglais sans équivalent, et les libellés anglais volontaires
  du gabarit (« 🇬🇧 English version below », « 🇬🇧 English version »).

## Décisions structurantes (ADR)

Les décisions de ce dépôt sont tracées dans `docs/decisions/`, selon la convention `D-NNN` du
kit `ct-ai-adr-management` (adoptée par [D-002](docs/decisions/D-002-convention-adr-et-tracage-retroactif.md),
emplacement amendé par [D-013](docs/decisions/D-013-amendement-d002-alignement-gabarit-de-depot.md)) :

- **Index** : `docs/decisions/README.md` — une ligne par ADR, plus la table des patterns
  inscrits.
- **Méthodologie** : `docs/decisions/adr-guide.md` — quand ouvrir un ADR, les 3 types, les
  workflows de création et d'amendement, les anti-patterns. **À lire avant d'en ouvrir un.** C'est
  une copie verbatim du kit : ne pas la retoucher (ses mentions de `scripts/`, de la CI
  `adr-check` et du schema JSON décrivent le kit, pas ce dépôt, qui les a écartés).
- **Templates** : `_template_cadrage.md`, `_template_closure.md`, `_template_amendement.md`.

Règles de travail à respecter ici :

- **Un ADR = un commit**, index mis à jour au même commit. Message : `docs(decisions): <slug>
  (D-NNN)`.
- **Jamais renuméroter** un ADR publié ; jamais réécrire le corps d'un ADR publié. Un amendement
  est un nouvel ADR (`type: amendement`, `amends: D-YYY`) qui met à jour `amended_by` et `status`
  de sa cible **au même commit**, sans toucher son corps.
- **Anciens chemins dans les corps d'ADR** : D-001 à D-012 citent `documentation/` et
  `documentation/adr/`, emplacements antérieurs à [D-013](docs/decisions/D-013-amendement-d002-alignement-gabarit-de-depot.md).
  Les lire comme `docs/` et `docs/decisions/` ; ne pas les « corriger » (corps figés).
- **Vérifier les prémisses dans le contenu de la skill**, pas dans un ADR antérieur : citer
  `fichier:ligne`.
- **Ne jamais fabriquer une citation déclencheuse ni des minutes de décision.** Les minutes sont
  les arbitrages de l'opérateur, pas les tiens. Un ADR sans déclencheur conversationnel cite
  l'artefact — c'est le régime d'antériorité posé par D-002 pour D-003 à D-012, dont les minutes
  d'origine sont déclarées indisponibles.
- **Identifiants locaux au dépôt** : un `D-NNN` cité sans nom de dépôt désigne celui d'ici. Les
  dépôts voisins (`ct-epi-visual`, `ct-fwk-specs-fonct`) ont leur propre série ; toute référence
  croisée se qualifie (« `ct-epi-visual` D-005 »).
- Le `CHANGELOG.md` versionne le **gabarit email**, pas les décisions : ne pas y lister d'ADR.

## Tests

- **`tests/tests_unitaires/`** — tests unitaires en `.py` (pytest), **uniquement si la skill
  embarque des scripts** (`skill/scripts/`). Ils testent le code, pas le comportement de Claude.
  La skill n'a pas de script : le dossier reste vide.
- **`tests/empirical_tests/`** — tests **à jouer manuellement par un humain** dans Claude AI : un
  prompt, le contexte fourni, le comportement attendu (déclenchement opt-in, cinq questions de
  cadrage, validation du brouillon texte, rendu du `.html` contre la checklist de
  `docs/PREREQUIS_TECHNIQUES.md`). Les rejouer avant chaque release et consigner le résultat dans
  `CHANGELOG.md`, § « 🔬 Validé empiriquement ». Aucun test n'y est encore écrit.
- `tests/` est **hors de `skill/`** : il n'est pas embarqué dans le zip de release.

## Duplications et maintenance

`docs/MAINTENANCE_GABARIT.md` porte la **carte des sources de vérité** : quels fichiers toucher
pour chaque type d'évolution, quelles valeurs sont dupliquées volontairement (les couleurs et les
dimensions figurent à la fois dans `SKILL.md` et dans les fichiers HTML), la **dépendance non
déclarée à la charte `ct-epi-visual`**, les cinq pièges de maintenance et les **divergences
connues** (deux assets sur trois inutilisés, aucun contrôle automatique, libellés anglais
volontaires… — les n° 1, 2 et 5 sont résolues en 2.0.0).
Les divergences y sont **numérotées de façon stable** : une entrée résolue est marquée résolue,
jamais retirée ni renumérotée.

La **procédure d'évolution** (carte → vérification `ct-epi-visual` → email de test et tests
empiriques → ADR → `CHANGELOG.md` → version du `README.md` → release) est au même endroit,
§ « Procédure d'évolution » : ne pas la recopier ici.

Toute évolution se répercute dans `CHANGELOG.md` (SemVer appliqué au **gabarit email** : une
couleur de bandeau ou un placeholder qui change est **MAJEUR**), avec ses deux vues —
synthétique et technique — et dans le numéro de version du `README.md`.
