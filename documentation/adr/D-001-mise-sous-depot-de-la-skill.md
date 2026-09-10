---
id: D-001
title: "Cadrage — mise sous dépôt de la skill epi-com-interne, dans son propre dépôt"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources: []
patterns:
  - livrable-autonome-doc-hors-livrable
  - distribution-par-release-taggee
  - un-depot-par-skill
---

## Contexte / déclencheur

> « sur la base du travail fait sur la skill ici, fait le aussi sur
> cette skill C:\Users\…\epi-com-interne. Il aura son propre repo
> github »

La skill `epi-com-interne` existait et était utilisée sans dépôt : pas
d'historique, pas de version installée identifiable, pas de canal de
distribution reproductible, et des choix de conception connus seulement
de leur auteur — dont une **règle d'exécution non évidente** (interdire
la réécriture des blobs base64, cf. [D-007]) qu'aucun commentaire ne
justifie hors de `SKILL.md`.

État courant (pré-décision, vérifié) :

- Aucun `.git` : `git rev-parse --show-toplevel` dans le dossier →
  *« not a git repository »*.
- La skill contient **sept fichiers** : `SKILL.md` (207 l.),
  `assets/template.html` (107 l.),
  `assets/banner-fallback-snippets.html` (90 l., dont deux data URI
  base64), `references/editorial-patterns.md` (136 l.) et trois PNG
  (`logo-e-white.png` et `logo-e-turquoise.png` en 80×80,
  `logo-e-original.png` en 213×120). ~52 Ko au total.
- Deux dépôts de skills Epiconcept avaient déjà résolu le même problème
  avec la même organisation : `ct-epi-visual` puis `ct-fwk-specs-fonct`
  — `skill/` + `documentation/` + `.github/workflows/release.yml`
  zippant `skill/` sur `release: published`.

**Question centrale** : *« quelle structure de dépôt pour la skill
`epi-com-interne`, dans quel dépôt, et comment la distribuer, sans
toucher au contenu de la skill ? »*

## Décisions actées

### Axe 1 — un dépôt par skill, pas un dépôt de skills

L'opérateur l'a tranché en une phrase : *« Il aura son propre repo
github »*. La skill ne rejoint pas `ct-epi-visual` ni
`ct-fwk-specs-fonct` : elle a son dépôt, `ct-epi-com-interne`.

La justification est le **cycle de vie**, pas la taille. Chaque skill a
sa version, sa release et son `.zip` installable ; un dépôt commun
imposerait de tagger l'ensemble pour publier une seule skill, et le
`.zip` de release ne pourrait plus cibler un chemin unique. Le
versionnement de trois gabarits indépendants dans un seul `CHANGELOG.md`
n'aurait pas de sens non plus — le turquoise d'un bandeau email et la
structure d'une spec fonctionnelle n'évoluent pas ensemble.

Écarté : monorepo de skills — mutualise la documentation ADR au prix du
couplage des releases. Écarté : sous-module Git — complexité sans
bénéfice pour des dépôts documentaires.

Corollaire : les identifiants `D-NNN` sont **locaux au dépôt**. Le
`D-003` d'ici (opt-in de la com interne) et celui de `ct-epi-visual`
(opt-in de la charte) sont deux décisions distinctes qui portent le même
numéro ; toute référence croisée se qualifie.

Pattern *« un-depot-par-skill »*.

### Axe 2 — reprendre la convention `ct-epi-visual` / `ct-fwk-specs-fonct`

Le dépôt n'invente rien : `README.md`, `CHANGELOG.md`, `CLAUDE.md`,
`.gitignore`, `.github/workflows/release.yml`, `documentation/`
(prérequis, guide de maintenance, `adr/`), `skill/`. Nom préfixé
`ct-`.

C'est le troisième dépôt de skill à adopter cette organisation. Un
mainteneur qui connaît l'un sait naviguer les autres — c'est le seul
bénéfice attendu, et il suffit.

### Axe 3 — `skill/` est le livrable, et il est autonome

Le dossier de la skill est renommé `epi-com-interne/` → `skill/`
(contenu **non modifié**), pour que le workflow de release cible un
chemin stable. Le nom d'invocation vient du champ `name:` de la
frontmatter (`skill/SKILL.md:2`), pas du nom du dossier : le renommage
est sans effet sur `/epi-com-interne`.

`skill/` ne référence jamais `documentation/`. Un `.zip` de `skill/`
seul doit être fonctionnel — ce qui suppose que les trois PNG et les
deux HTML y soient, blobs base64 compris.

Pattern *« livrable-autonome-doc-hors-livrable »*.

### Axe 4 — `documentation/` n'est pas embarqué

Prérequis techniques, guide de maintenance et décisions servent au
mainteneur, pas à la production d'un email. Les embarquer serait du
contexte gaspillé — et les « divergences connues » s'y liraient comme
des consignes alors qu'elles sont des notes de maintenance.

### Axe 5 — la distribution passe par les releases GitHub

`.github/workflows/release.yml` se déclenche sur `release: published`,
zippe `skill/` en excluant `__pycache__`, `*.pyc` et `node_modules`, et
attache `skill.zip` à la release. Un tag ↔ un `.zip` ↔ un état du
gabarit.

Le `.gitignore` exclut les livrables d'essai — `com-interne-*.html`,
mais aussi les **fragments temporaires** de l'assemblage
(`content_fr.html`, `content_en.html`, `signature.html`, `header.html`,
`footer.html`, `divider.html`, cf. [D-007]) : ces fichiers portent du
contenu de communication réel et n'ont rien à faire dans le dépôt du
gabarit.

Écarté : distribution par clone — pas de notion de version installée.

Aucun gestionnaire de paquets, aucun build, aucune suite de tests,
aucun linter : le dépôt contient du Markdown, du HTML et des PNG. Le CI
ne fait que l'empaquetage.

Pattern *« distribution-par-release-taggee »*.

## Conditions de légitimité

1. **Autonomie de `skill/`** : `grep -r "documentation/" skill/` doit
   rester vide. Une référence sortante rendrait le `.zip` incomplet.
2. **Intégrité du contenu migré** : le contenu de la skill est
   identique à l'avant-migration (renommage de dossier seul), **blobs
   base64 bit-à-bit inclus** — une copie qui les altérerait casserait
   les bandeaux sans que rien ne le signale.
3. **Chemins absolus** : le renommage `epi-com-interne/` → `skill/`
   casse tout chemin absolu qui pointait vers l'ancien dossier. Le
   chemin d'exécution documenté dans la skill
   (`/mnt/skills/user/epi-com-interne/assets`, `SKILL.md:76`) désigne le
   dossier **installé**, nommé par la frontmatter : il n'est pas
   affecté.
4. **Le dépôt ne contient pas d'emails produits** : le gabarit, pas les
   livrables. Une com interne réelle versionnée ici serait du bruit et
   une diffusion non voulue d'information interne.
5. **Périmètre de diffusion** : le `.zip` de release embarque le logo
   Epiconcept — actif de marque. La release reste dans le périmètre
   interne (dépôt privé).

## Conséquences

- Dépôt initialisé sur `main`, commit initial couvrant la structure
  complète.
- `README.md` (principe, étapes, périmètre, prérequis, sécurité),
  `CHANGELOG.md` (SemVer appliqué au gabarit), `CLAUDE.md`,
  `.gitignore`, `documentation/PREREQUIS_TECHNIQUES.md`,
  `documentation/MAINTENANCE_GABARIT.md`.
- Création du dépôt distant et push : **laissés à l'opérateur**, comme
  pour les deux dépôts précédents.
- Les choix de conception antérieurs de la skill restent à tracer —
  objet de [D-002](D-002-convention-adr-et-tracage-retroactif.md).

## Sources

Internes : aucun ADR amont (premier ADR du dépôt).
Externes : `Epiconcept-Paris/ct-epi-visual` et
`Epiconcept-Paris/ct-fwk-specs-fonct` — structure de dépôt,
`documentation/`, convention ADR et `release.yml` pris pour modèle ;
`ct-ssi-tableau-de-bord`, origine de cette convention.

## Minutes de décision

**Q1 (structure et emplacement)** : *« sur la base du travail fait sur
la skill ici, fait le aussi sur cette skill […] Il aura son propre repo
github »* → **convention des deux dépôts précédents reprise
intégralement**, dans un **dépôt dédié** `ct-epi-com-interne`.
Justification : consigne explicite sur les deux points.

**Q2 (contenu de la skill)** : arbitré par analogie avec les deux
dépôts précédents, dont la consigne d'origine était *« Ne touche à rien
à niveau de contenu de la skill »* → **contenu inchangé**. Les écarts
relevés en lecture (inventaire des logos contradictoire entre
`SKILL.md:164-166` et `SKILL.md:205-206` ; taille du footer divergente
entre `SKILL.md:154` / `template.html` et
`references/editorial-patterns.md:128` ; deux assets sur trois
inutilisés) ont donc été **documentés, non corrigés** — cf.
`documentation/MAINTENANCE_GABARIT.md`. À la différence de
`ct-fwk-specs-fonct`, aucune correction n'a été demandée dans cette
session.

**Q3 (création du dépôt distant)** : non posée. Les deux dépôts
précédents ont tranché — *« non, je le ferai moi »* — et la même règle
est appliquée par défaut : le dépôt reste local, prêt à être poussé par
l'opérateur.
