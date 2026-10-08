---
id: D-012
title: "Cadrage — Claude AI comme environnement cible, chemins et outils assumés"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-001
  - D-006
  - D-007
patterns:
  - environnement-cible-unique-assume
---

## Contexte / déclencheur

> « la skill est principalement utilsable dans claude ai »

Cette phrase de l'opérateur portait sur `fwk-specs-fonct`, dans la même
session — elle y a produit `ct-fwk-specs-fonct` D-013. Elle est
**appliquée ici par analogie**, et cet ADR le déclare : le déclencheur
est authentique, mais il n'a pas été posé sur cette skill-ci. À
confirmer par l'opérateur ; à rouvrir si sa réponse diffère pour
`epi-com-interne`.

Le contexte est la relecture de mise sous dépôt. La skill dépend de son
environnement d'exécution **plus profondément** que les deux autres
dépôts de skills : l'assemblage lui-même suppose un système de fichiers
et un shell ([D-007]).

État courant (pré-décision, vérifié sur le poste de l'opérateur) :

| Ligne | Chemin / outil | Rôle |
|-------|----------------|------|
| `SKILL.md:26` | outil `ask_user_input_v0` | les six questions de cadrage ([D-006]) |
| `SKILL.md:60-63` | `/home/claude/content_fr.html`, `content_en.html`, `signature.html` | fragments éditoriaux écrits par le modèle |
| `SKILL.md:66-71` | `/home/claude/header.html`, `footer.html`, `divider.html`, `email.html` | intermédiaires d'assemblage |
| `SKILL.md:76`, `:89` | `/mnt/skills/user/epi-com-interne/assets` | lecture du template et des snippets |
| `SKILL.md:72`, `:104` | `/mnt/user-data/outputs/com-interne-<slug>.html` | livrable final |
| `SKILL.md:120` | outil `present_files` | présentation du livrable |
| `SKILL.md:56`, `:109`, `:114` | outils `bash_tool`, `create_file` | assemblage et écriture des fragments |

- `ls /mnt` → **le répertoire n'existe pas** sur le poste. Les skills
  sont installées sous `~/.claude/plugins/marketplaces/…`, et
  `present_files`, `ask_user_input_v0`, `bash_tool`, `create_file` ne
  sont pas les outils de Claude Code.
- Conséquence : hors Claude AI, la skill ne trouve ni le template ni
  les snippets, et écrit dans des chemins inexistants.

**Question centrale** : *« la skill vise-t-elle Claude AI, en assumant
ses chemins et ses outils, ou doit-elle être neutre en
environnement ? »*

## Décisions actées

### Axe 1 — Claude AI est l'environnement cible, et les chemins restent

Les chemins et les noms d'outils sont **conservés tels quels**. Ce ne
sont pas des oublis à corriger mais l'adaptation de la skill à
l'environnement où elle est réellement utilisée.

Le gain est concret et perdu par toute neutralisation : les six
questions groupées en un appel `ask_user_input_v0` ([D-006], axe 1), la
livraison sans étape manuelle via `/mnt/user-data/outputs/` +
`present_files`, et surtout un **squelette bash exécutable tel quel**
(`SKILL.md:74-106`) avec des chemins littéraux. Un exemple à chemins
variables serait à réinterpréter à chaque exécution — exactement ce que
[D-007] cherche à éviter en imposant une méthode plutôt qu'un principe.

Écarté : formulation neutre (« écrire dans le répertoire de travail »,
« charger les assets depuis leur emplacement d'installation ») — porte
un usage minoritaire au prix de l'usage principal, et remplace un
squelette copiable par une consigne à interpréter. Écarté : double
procédure conditionnelle (« si Claude AI… sinon… ») — alourdit un
fichier de règles déjà long, pour un branchement que la skill ne peut
pas évaluer de façon fiable.

Pattern *« environnement-cible-unique-assume »*.

### Axe 2 — la dépendance dure n'est pas le chemin, c'est le shell

Il faut distinguer deux niveaux :

- les **chemins** (`/mnt/…`, `/home/claude/`) et les **outils**
  d'interaction (`ask_user_input_v0`, `present_files`) sont
  transposables : un autre répertoire, une question posée en
  conversation, une livraison par le mécanisme de l'hôte ;
- l'existence d'un **système de fichiers et d'un shell** avec `awk`,
  `sed` et `python3` ne l'est pas. Sans elle, [D-007] est inapplicable
  — il n'existe aucune stratégie de repli qui respecte l'interdiction de
  réécrire les blobs base64.

C'est la vraie contrainte d'environnement de cette skill, et elle est
plus forte que celle des dépôts voisins : `fwk-specs-fonct` produit du
Markdown que le modèle peut écrire directement, `epi-com-interne` ne
peut pas.

### Axe 3 — l'exécution hors Claude AI est un mode dégradé documenté

Elle reste possible sous les trois transpositions de l'axe 2, écrites
**dans la documentation du dépôt**
(`documentation/PREREQUIS_TECHNIQUES.md`, section « Environnement
d'exécution »), pas dans la skill — cohérent avec [D-001] axe 4 :
`documentation/` sert le mainteneur, la skill sert l'exécution.

Le mode d'échec, lui, est **franc** et c'est une chance : sans le
template ni les snippets, l'assemblage ne produit rien d'exploitable.
Contrairement à d'autres dépendances de cette skill — un placeholder non
substitué ([D-005]), un blob altéré ([D-007]), la troncature Gmail
([D-008]) — celle-ci ne dégrade pas silencieusement un livrable
diffusable.

### Axe 4 — toute modification de ces chemins passe par un amendement

Comme la `description` de la frontmatter ([D-003]) et la méthode
d'assemblage ([D-007]), ces lignes sont protégées : les rendre portables
serait revenir sur la présente décision, pas la préciser.
`documentation/MAINTENANCE_GABARIT.md` renvoie explicitement vers un ADR
d'amendement.

## Conditions de légitimité

1. **L'usage reste majoritairement sur Claude AI** : c'est la prémisse.
   Elle est ici **héritée par analogie** et non confirmée pour cette
   skill — première chose à vérifier avant de s'appuyer sur cet ADR.
2. **Les chemins et outils de Claude AI restent stables** : ils
   dépendent d'une plateforme tierce. Un renommage
   (`ask_user_input_v0` versionné, `present_files` retiré,
   `/mnt/skills/user/` déplacé) casse la skill sans avertissement — le
   suffixe `_v0` du nom d'outil signale d'ailleurs une interface non
   figée.
3. **Un shell reste disponible** : dépendance dure de l'axe 2. Sa perte
   ne dégraderait pas la skill, elle l'invaliderait.
4. **Aucune neutralisation partielle** : corriger un chemin sur sept
   produirait une skill à moitié portable, fiable dans aucun
   environnement.
5. **La transposition reste documentée** : si les trois points de l'axe
   2 cessent d'être écrits quelque part, le mode dégradé devient
   inaccessible.

## Conséquences

- `skill/SKILL.md` **n'est pas modifié** par cette décision : elle
  entérine l'existant ([D-001], Q2).
- `README.md` nomme Claude AI comme environnement cible dans les
  prérequis ; `CLAUDE.md` en fait un invariant ;
  `documentation/PREREQUIS_TECHNIQUES.md` porte la transposition ;
  `documentation/MAINTENANCE_GABARIT.md` l'inscrit en cinquième piège
  plutôt que dans ses « divergences connues » — ce n'est pas un défaut
  du gabarit, c'est la contrepartie d'un choix.
- La condition de légitimité 3 de [D-007] (« disponibilité d'un
  shell ») trouve ici son cadre : la dépendance est assumée, pas
  résolue.

## Sources

Internes : [D-001] (frontière skill / documentation), [D-006] (outil de
question groupée), [D-007] (assemblage par le shell — dépendance dure),
[D-003] (précédent de règle protégée par amendement).
Externes : environnement d'exécution des skills sur claude.ai
(`/mnt/skills/`, `/home/claude/`, `/mnt/user-data/outputs/`, outils
`bash_tool`, `create_file`, `ask_user_input_v0`, `present_files`) ;
`Epiconcept-Paris/ct-fwk-specs-fonct` D-013 — même arbitrage, tranché
par l'opérateur sur une autre skill.

## Minutes de décision

**Q1 (neutralité d'environnement ?)** : **non posée sur cette skill.**
La réponse de l'opérateur sur `fwk-specs-fonct` dans la même session —
*« la skill est principalement utilsable dans claude ai »* — a été
**appliquée par analogie**, la dépendance à l'environnement étant ici
plus forte encore (axe 2). Conformément à [D-002] (axe 4), l'emprunt est
déclaré plutôt que présenté comme un arbitrage propre à
`epi-com-interne` : à confirmer, et à rouvrir si la réponse diffère.

**Q2 (contenu de la skill)** : aucune correction demandée dans cette
session, contrairement à `ct-fwk-specs-fonct` où le renommage
`epiconcept-style` → `epi-visual` avait été explicitement demandé. Les
écarts relevés ici (inventaire des logos, taille du footer, assets
inutilisés) sont **documentés, non corrigés** — cf. [D-001], Q2, et
`documentation/MAINTENANCE_GABARIT.md`.
