---
id: D-016
title: "Amendement D-003 — description alignée sur la structure sans bandeau bas, déclenchement inchangé"
status: accepted
type: amendement
date: 2026-10-08
amends: D-003
supersedes: []
amended_by: []
sources:
  - D-003
  - D-014
patterns: []
---

## Déclencheur de l'amendement

> « Le bandeau en bas n'est pas nécessaire »

puis : *« applique 1-5. C'est non pour 6 et 7 »* — cf. [D-014].

La `description` de la frontmatter (`skill/SKILL.md:3`) énumérait la
structure produite : *« bandeau haut, contenu mono/bilingue FR/EN,
bandeau bas, footer »*. Après [D-014], elle annoncerait un bloc qui
n'existe plus. D-003, axe 3, impose qu'une modification de ce champ
passe par un amendement, quelle qu'en soit la portée.

## Modification actée

### Axe 3 de D-003 (impacté) — texte de la frontmatter

**Avant** : *« … : bandeau haut, contenu mono/bilingue FR/EN, bandeau
bas, footer. »*

**Après** : *« … : bandeau haut, contenu mono/bilingue FR/EN,
signature, footer. »*

**Justification du changement** : l'énumération décrit le livrable, et
le livrable a changé. Seuls ces quatre mots sont touchés.

## Sections D-003 impactées vs préservées

- **Impacté** : la phrase descriptive en tête de la `description`.
- **Préservé, mot pour mot** : tout le dispositif de déclenchement — le
  ⚠️ « NE PAS déclencher automatiquement », les deux voies (a) / (b),
  les alias, la question type, « AVANT […] jamais après », l'interdit
  du retraitement a posteriori, le défaut sans la skill. Axes 1 et 2
  inchangés.
- **Conditions de légitimité** : inchangées. Si un déclenchement
  automatique était observé après cette modification, la condition 1
  s'appliquerait comme avant.

## Conséquences

- `skill/SKILL.md:3` : un seul remplacement, « bandeau bas » →
  « signature ».

## Sources

Internes : [D-003] (axe 3), [D-014] (structure).

## Minutes de décision

**Q1 (toucher ou non la description)** : arbitré sans question à
l'opérateur — laisser « bandeau bas » aurait décrit un livrable faux ;
la modification est limitée à l'énumération, le dispositif d'opt-in
n'est pas reformulé.
