---
id: D-002
title: "Cadrage — adoption de la convention ADR et traçage rétroactif du gabarit"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-001
patterns:
  - anteriorite-ancree-sur-artefact
---

## Contexte / déclencheur

> **Déclencheur dérivé de la consigne d'ensemble** —
> *« sur la base du travail fait sur la skill ici, fait le aussi sur
> cette skill […] »* : le travail pris pour référence
> (`ct-fwk-specs-fonct`, lui-même calqué sur `ct-epi-visual`) comprend
> `documentation/adr/` avec le guide, les trois templates, un index et
> treize ADR. La consigne de similarité porte donc aussi sur la
> convention de traçage des décisions.

La skill `epi-com-interne` porte une douzaine de choix structurants —
déclenchement, format de livrable, méthode d'assemblage, règle de
couleur, conventions éditoriales, bilingue — dont aucun n'est expliqué
là où il s'applique. Le cas le plus net est [D-007] : `SKILL.md:54`
interdit de réécrire les blobs base64 en donnant son motif (coût en
tokens), mais rien ne dit pourquoi la méthode d'assemblage par le shell
a été retenue plutôt qu'une autre.

État courant (pré-décision, vérifié) :

- `ct-fwk-specs-fonct/documentation/adr/` fournit : `adr-guide.md`
  (417 l.), trois templates (`_template_cadrage.md`,
  `_template_closure.md`, `_template_amendement.md`), un `README.md`
  d'index à table `ID | Titre | Status | Type | Date` + table des
  patterns, et treize ADR `D-NNN`.
- Ce dossier est lui-même une adoption **minimale** du kit
  `ct-ai-adr-management` : ni `adr_new.py`, ni `adr_check.py`, ni CI
  `adr-check`, ni schema JSON.
- Le guide autorise explicitement l'adaptation du chemin :
  *« les ADR vivent sous `docs/decisions/` (chemin recommandé ; adaptez
  si besoin, mais un seul dossier plat) »*.
- La skill à tracer ne contient **aucun code exécutable** : les
  décisions s'ancrent sur des lignes de Markdown et de HTML.

**Question centrale** : *« sous quelle forme adopter la convention
`D-NNN`, et comment tracer honnêtement des décisions prises avant
l'existence du dépôt ? »*

## Décisions actées

### Axe 1 — adopter le kit en version minimale, en consommateur normal

Copie verbatim depuis `ct-fwk-specs-fonct/documentation/adr/` (elle-même
copie de `ct-epi-visual`, elle-même copie du kit) : `adr-guide.md` et
les trois templates. Pas de scripts, pas de CI, pas de schema JSON — le
dépôt est petit et documentaire, le formalisme reste proportionné.

Le guide est copié **sans retouche**, y compris ses références aux
scripts, à la CI et au schema non adoptés ici, et son chemin
`docs/decisions/`. Le modifier créerait un fork à resynchroniser à
chaque évolution du kit — et, ici, un fork de fork.

### Axe 2 — les ADR restent dans `documentation/adr/`

Même arbitrage que dans les deux dépôts précédents : [D-001] a acté
l'alignement sur leur convention, qui place toute la documentation de
référence sous `documentation/`. Contrainte du guide respectée : **un
seul dossier plat**.

### Axe 3 — numérotation de `D-001` à `D-012`, locale au dépôt

Les identifiants sont locaux ([D-001], axe 1). Le `D-003` d'ici, celui
de `ct-epi-visual` et celui de `ct-fwk-specs-fonct` sont trois
décisions distinctes qui portent le même numéro dans trois dépôts —
c'est le fonctionnement normal de la convention, et la raison pour
laquelle une référence inter-dépôts se cite toujours qualifiée
(`ct-epi-visual` D-003).

Coïncidence à ne pas surinterpréter : les trois dépôts ont un `D-003`
« déclenchement opt-in strict ». Ce n'est pas une convention partagée,
c'est le même dispositif retrouvé dans trois skills, tracé en troisième
position parce que D-001 et D-002 sont toujours la mise sous dépôt et
l'adoption de la convention.

### Axe 4 — antériorité : ancrage sur l'artefact, jamais de citation fabriquée

[D-003] à [D-012] tracent des décisions **antérieures au dépôt**, dont
ni la date réelle ni la conversation d'origine ne sont connues. Le guide
interdit de fabriquer une citation déclencheuse (anti-patterns) et
prévoit l'alternative : *« référence à l'artefact déclencheur si non
conversationnel »*. Donc, pour ces dix ADR :

- le déclencheur cite **l'artefact vérifié** — fichier et ligne où la
  règle est inscrite — jamais une pseudo-citation de l'opérateur ;
- `date:` est la date du **traçage** (2026-09-09), pas une date d'acte
  inventée ; chaque ADR le dit en clair ;
- la section **Minutes de décision** ne reconstitue aucun arbitrage :
  elle indique que les minutes d'origine ne sont pas disponibles.

Le risque est aggravé ici par la nature du contenu : `SKILL.md` est
écrit à l'impératif, avec des ⚠️ et des « JAMAIS » en majuscules. Il est
tentant de lire ces emphases comme la trace d'un incident vécu et de
raconter l'incident. Une règle inscrite atteste que la règle existe,
**pas** de l'événement qui l'a produite. Quand `SKILL.md` donne
lui-même un motif chiffré (« ~5 000 caractères », « ralentit à
l'extrême »), ce motif est cité comme **texte de la règle**, pas comme
compte-rendu d'expérience.

Pattern *« anteriorite-ancree-sur-artefact »*.

### Axe 5 — `CHANGELOG.md` conservé, périmètre disjoint

Le guide proscrit un `CHANGELOG.md` maintenu à la main *pour les
décisions* — le frontmatter porte déjà `date` et `status`. Le
`CHANGELOG.md` du dépôt versionne le **gabarit email** (structure,
placeholders, couleurs), pas les décisions : périmètres disjoints. Il
est conservé, et ne liste pas les ADR.

## Conditions de légitimité

1. **Index synchronisé** : `documentation/adr/README.md` liste
   exactement les fichiers `D-*.md` présents. Contrôlé à la main ; si
   le rythme rend ce contrôle pénible, activer `adr_check.py` + CI
   plutôt qu'abandonner la discipline.
2. **Guide non forké** : `adr-guide.md` reste identique à sa source
   (`diff` avec `ct-fwk-specs-fonct` doit être vide).
3. **Authenticité** : aucune citation déclencheuse ni minute de
   décision de ce dossier n'est fabriquée, et aucun incident n'est
   reconstitué à partir d'une emphase typographique.
4. **Un ADR = un commit** à partir de maintenant. La création initiale
   du dossier est l'exception prévue par le guide : une migration
   massive explicitement cadrée — par le présent ADR.
5. **Références inter-dépôts qualifiées** : un `D-NNN` cité sans nom de
   dépôt désigne celui d'ici.

## Conséquences

- `documentation/adr/` : `adr-guide.md` + trois templates copiés,
  `README.md` d'index au format du kit, `D-001`…`D-012` écrits.
- `CLAUDE.md` porte un bloc d'instructions IA pointant vers le guide et
  l'index, et la liste des invariants rattachés à leurs ADR.
- `README.md`, `CHANGELOG.md`, `documentation/PREREQUIS_TECHNIQUES.md`
  et `documentation/MAINTENANCE_GABARIT.md` référencent les décisions
  par `D-NNN`.

## Sources

Internes : [D-001] (structure du dépôt, un dépôt par skill,
alignement `documentation/`).
Externes : `Epiconcept-Paris/ct-fwk-specs-fonct` et
`Epiconcept-Paris/ct-epi-visual` — `documentation/adr/` pris pour
modèle ; kit `ct-ai-adr-management` dont ce dossier est issu.

## Minutes de décision

**Q1 (forme d'adoption)** : dérivée de la consigne de similarité →
**convention adoptée en version minimale** (guide + templates + index,
sans scripts ni CI), par copie verbatim. Justification : les dossiers
pris pour modèle sont eux-mêmes des adoptions minimales du même kit.

**Q2 (emplacement)** : arbitré sans question à l'opérateur —
`documentation/adr/` conservé plutôt que `docs/decisions/`. Le guide
autorise l'adaptation du chemin, et [D-001] a déjà acté
`documentation/` comme dossier de référence du dépôt.

**Q3 (traçage rétroactif)** : arbitré sans question à l'opérateur, sous
contrainte du guide — pas de citation déclencheuse fabriquée, pas de
minutes reconstituées, pas d'incident inventé derrière un
avertissement, dates de traçage assumées comme telles.
