---
id: D-015
title: "Amendement D-009 — turquoise réservé au seul bandeau haut, footer en 11px #999999"
status: accepted
type: amendement
date: 2026-10-08
amends: D-009
supersedes: []
amended_by: []
sources:
  - D-009
  - D-014
patterns: []
---

## Déclencheur de l'amendement

> « Le bandeau en bas n'est pas nécessaire »

puis : *« applique 1-5. C'est non pour 6 et 7 »* — cf. [D-014], qui
supprime le bandeau bas de la structure.

Sans bandeau bas, l'axe 3 de D-009 (symétrie haut/bas, « bookend
visuel ») n'a plus d'objet, et la règle « turquoise en fond des deux
bandeaux » ne désigne plus qu'une zone.

## Modification actée

### Axe 1 de D-009 (impacté) — zone du turquoise

**Avant** : `#4BCDDB` en fond des **deux** bandeaux, nulle part
ailleurs.

**Après** : `#4BCDDB` en fond du **bandeau haut** uniquement. Toujours
ni bloc intermédiaire coloré, ni dégradé, ni bandeau en bas.

### Axe 3 de D-009 (supprimé) — symétrie haut/bas

**Avant** : deux bandeaux de même turquoise, le bas plus compact ; le
même logo blanc en haut et en bas.

**Après** : sans objet. Le logo blanc ne sert plus qu'au bandeau haut.

### Footer légal (précision de couleur)

**Avant** : 10px `#bbbbbb` (`SKILL.md` et `template.html`), en
contradiction avec `references/editorial-patterns.md:128` (11-12px
`#999999`) — divergence connue n° 2.

**Après** : **11px `#999999`** partout. `#bbbbbb` à 10px se lisait mal,
et dans une carte blanche sans bandeau pour le délimiter, le footer
doit rester lisible pour être perçu comme partie de l'email.

**Justification du changement** : suppression du bandeau bas actée par
l'opérateur ; alignement du footer sur la valeur déjà portée par la
référence éditoriale.

## Sections D-009 impactées vs préservées

- **Impacté** : axe 1 (une seule zone), axe 3 (supprimé).
- **Préservé** : axe 2 (bleu foncé = texte), axe 4 (logo officiel,
  jamais redessiné), axe 5 (la charte `ct-epi-visual` fait autorité).
- **Préservé, à noter** : le trait du divider FR/EN
  (`banner-fallback-snippets.html`, snippet `DIVIDER`) est un
  `border-top` turquoise. C'est un trait, pas un fond : il existait
  avant cet amendement et n'est pas modifié ici.
- **Conditions de légitimité** : condition 1 se lit « aucun turquoise
  hors du bandeau haut » ; condition 2 (symétrie) est sans objet ;
  conditions 3 à 5 inchangées.

## Conséquences

- `skill/SKILL.md` : tableau des couleurs, section « Bandeau du bas »
  supprimée, section footer réécrite, inventaire des logos aligné
  (`logo-e-turquoise.png` en réserve — résout la divergence n° 1).
- `skill/assets/template.html` et `references/editorial-patterns.md` :
  footer 11px `#999999` (résout la divergence n° 2).
- Pas de couleur nouvelle : `#999999` figurait déjà dans la palette des
  snippets et dans la référence éditoriale. Rien à reporter dans
  `ct-epi-visual`.

## Sources

Internes : [D-009] (axes 1 et 3), [D-014] (structure).

## Minutes de décision

**Q1 (bandeau bas)** : *« Le bandeau en bas n'est pas nécessaire »* →
**supprimé**.

**Q2 (couleur du footer)** : proposée par Claude (11px `#999999`, valeur
de la référence éditoriale) dans la proposition 4 → **retenue** par
*« applique 1-5 »*.
