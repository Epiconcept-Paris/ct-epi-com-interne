---
id: D-013
title: "Amendement D-002 — ADR sous docs/decisions/, dépôt aligné sur le gabarit ct-skill-template"
status: accepted
type: amendement
date: 2026-10-08
amends: D-002
supersedes: []
amended_by: []
sources:
  - D-001
  - D-002
patterns:
  - structure-alignee-sur-le-gabarit-commun
---

## Déclencheur de l'amendement

> « Met à jour la structure du repo (sans rien changer à la skill en soi
> même) sur la base de la structure proposée par
> C:\Users\…\ct-skill-template\ct-skill-template »

Le dépôt `ct-skill-template` formalise désormais le squelette commun aux
dépôts de skills Epiconcept. Il place les ADR sous `docs/decisions/` —
le chemin recommandé par le guide — et ajoute un dossier `tests/`.
Son index d'ADR le déclare : *« Les dépôts de skills antérieurs
utilisaient `documentation/adr/` »*. Ce dépôt est l'un d'eux : [D-002],
axe 2, y avait conservé `documentation/adr/` par alignement sur les
dépôts voisins de l'époque. Le motif de l'axe 2 — s'aligner sur la
convention commune — conduit aujourd'hui à la conclusion inverse.

## Modification actée

### Axe 2 de D-002 (impacté)

**Avant** : les ADR restent dans `documentation/adr/` ; toute la
documentation de référence vit sous `documentation/`.

**Après** : les ADR vivent dans `docs/decisions/` (chemin recommandé
par le guide, sans adaptation) ; la documentation de référence vit sous
`docs/` (`docs/PREREQUIS_TECHNIQUES.md`, `docs/MAINTENANCE_GABARIT.md`).
Contrainte du guide toujours respectée : un seul dossier plat.

**Justification du changement** : même motif que l'axe 2 d'origine —
un mainteneur qui connaît un dépôt de skill sait naviguer les autres —
appliqué à la convention telle qu'elle est désormais portée par le
gabarit commun.

### Ajouts de structure, sans effet sur les axes de D-002

- `tests/tests_unitaires/` (vide : la skill n'embarque aucun script) et
  `tests/empirical_tests/` (tests de comportement à jouer à la main,
  aucun encore écrit), hors de `skill/`, donc hors du zip de release.
- `README.md`, `CLAUDE.md`, `CHANGELOG.md` et `.gitignore` réorganisés
  selon les sections du gabarit, à contenu équivalent.

## Sections D-002 impactées vs préservées

- **Impacté** : axe 2 (cf. ci-dessus).
- **Impacté, par conséquence de chemin** : condition de légitimité 1 —
  l'index synchronisé est désormais `docs/decisions/README.md`.
  Condition 2 — la source de comparaison du guide non forké devient
  `ct-skill-template/docs/decisions/adr-guide.md` (vérifié identique
  à la date de cet ADR, aux fins de ligne près).
- **Préservé** : axes 1, 3, 4 et 5 ; conditions 3, 4 et 5.

## Conséquences

- `git mv documentation/adr docs/decisions`, puis
  `documentation/PREREQUIS_TECHNIQUES.md` et
  `documentation/MAINTENANCE_GABARIT.md` vers `docs/`. Les liens
  relatifs entre ADR restent valides (même dossier) ; ceux des deux
  documents de `docs/` sont réécrits (`adr/` → `decisions/`).
- **Corps des ADR D-001 à D-012 non retouchés** : ils citent
  `documentation/` et `documentation/adr/`. Ces mentions sont à lire
  comme `docs/` et `docs/decisions/` ; la règle de lecture est déclarée
  dans l'index (§ « Adaptations locales ») et dans `CLAUDE.md`.
- [D-001], axe 2, énumère la structure d'origine (`documentation/`) :
  sa décision — reprendre la convention commune des dépôts de skills —
  est **préservée**, seule son énumération est datée. Pas d'amendement
  de D-001.
- **`skill/` inchangé** : aucun fichier de la skill n'est touché, et
  `skill/` ne référence ni `docs/` ni `tests/`.

## Sources

Internes : [D-001] (structure du dépôt, convention commune), [D-002]
(axe 2, emplacement des ADR).
Externes : `ct-skill-template` — `README.md`, `CLAUDE.md`,
`docs/decisions/README.md` (§ « Adaptations locales »).

## Minutes de décision

**Q1 (emplacement des ADR)** : dérivée de la consigne → `docs/decisions/`,
chemin du gabarit.

**Q2 (documents de maintenance)** : arbitré sans question à
l'opérateur — `PREREQUIS_TECHNIQUES.md` et `MAINTENANCE_GABARIT.md`
déplacés tels quels sous `docs/`, plutôt que fondus dans `CLAUDE.md`
comme le suggère le gabarit. Motif : onze ADR (D-001 à D-012, sauf D-006) y renvoient par leur nom,
et leurs corps sont figés ; garder les fichiers garde les renvois
lisibles.

**Q3 (tests)** : arbitré sans question à l'opérateur — dossiers créés
vides ; aucun test empirique n'est rédigé, faute de scénario validé par
l'opérateur.
