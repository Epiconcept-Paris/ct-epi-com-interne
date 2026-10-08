---
id: D-007
title: "Cadrage — assemblage par le shell : les blobs base64 ne passent jamais par le contexte"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
  - D-004
  - D-005
patterns:
  - donnee-incompressible-jamais-en-contexte
  - assemblage-hors-modele-fragments-seuls-generes
  - interdiction-enoncee-avec-son-chiffre
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefact : `skill/SKILL.md:54` :
> « ⚠️ **RÈGLE CRITIQUE — NE JAMAIS retaper les blobs base64.** Les
> snippets de bandeaux fallback contiennent le logo Epiconcept embarqué
> en base64 (~6 000 caractères par blob, deux blobs = ~12 000 caractères
> de bruit non-compressible). Les réécrire via `create_file` coûte des
> milliers de tokens, ralentit la génération à l'extrême et donne
> l'impression d'un blocage. » ;
> `skill/SKILL.md:56` : « **Méthode imposée : assembler via
> `bash_tool`** avec `cat` + `sed`. »

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

C'est la décision la plus spécifique de cette skill, et la seule que
`SKILL.md` qualifie lui-même de « critique ». Elle règle une collision
entre deux contraintes qui, prises séparément, sont raisonnables :

- l'email doit être **self-contained**, donc les logos sont embarqués en
  data URI ([D-004], axe 1) — Gmail strippant les `<svg>` inline
  (`SKILL.md:158`), un PNG en base64 est la seule option universelle ;
- le livrable est **un fichier unique** à écrire ([D-004], axe 1).

Écrire ce fichier par un appel d'écriture classique impose donc de
régénérer les ~12 000 caractères de base64 — un contenu qui n'a aucune
structure, qu'aucun modèle ne compresse, et dont la moindre altération
casse silencieusement l'image.

État courant (pré-décision, vérifié) :

- `banner-fallback-snippets.html` (90 l.) contient deux
  `src="data:image/png;base64,…"` : le logo 56×56 du bandeau haut
  (`:31`) et le logo 44×44 du bandeau bas (`:50`).
- `SKILL.md:58-73` détaille la procédure d'assemblage en huit points ;
  `SKILL.md:74-106` en donne un squelette bash exécutable, avec un
  `python3` en heredoc pour la substitution multi-ligne.
- `SKILL.md:108-116` liste explicitement trois interdits (❌) et trois
  obligations (✅).
- Les fragments que le modèle écrit sont **trois fichiers** :
  `content_fr.html`, `content_en.html`, `signature.html`
  (`SKILL.md:60-63`).

**Question centrale** : *« qui écrit le HTML final : le modèle ou le
shell ? »*

## Décisions actées

### Axe 1 — les données incompressibles ne passent pas par le contexte

Les blobs base64 restent **dans les fichiers source**, du disque au
livrable, sans jamais transiter par la génération du modèle.

C'est le principe général, et il ne dépend pas d'un budget de tokens
particulier : une donnée sans structure ne gagne rien à passer par un
modèle de langage, et risque d'y perdre — une altération d'un seul
caractère produit une image cassée que rien ne signale.

Pattern *« donnee-incompressible-jamais-en-contexte »*.

### Axe 2 — le modèle écrit les fragments, le shell assemble

Répartition stricte :

- **le modèle** produit `content_fr.html`, `content_en.html` et
  `signature.html` — du contenu éditorial, c'est-à-dire ce qu'il est
  seul à savoir faire ;
- **le shell** fait tout le reste : extraire les snippets, substituer
  `{{TITLE_FOR_BANNER}}`, injecter les fragments dans le template,
  remplacer les placeholders scalaires, écrire le fichier final.

Les trois interdits de `SKILL.md:108-111` sont les trois façons de
violer cette répartition : écrire le HTML complet, copier-coller le
contenu des snippets dans un appel d'outil, réécrire un data URI
« même partiellement ».

Écarté : appel d'écriture avec le HTML final — l'option naturelle, et
celle que la règle existe pour empêcher. Écarté : logos référencés par
URL externe — supprimerait les blobs, mais rompt l'autonomie du livrable
([D-004], axe 1) et expose au blocage des images distantes. Écarté :
logos en `<svg>` inline — Gmail les strippe (`SKILL.md:158`).

Pattern *« assemblage-hors-modele-fragments-seuls-generes »*.

### Axe 3 — l'interdiction est énoncée avec son chiffre et son symptôme

`SKILL.md:54` ne dit pas « ne pas réécrire les blobs ». Il dit combien
(~6 000 caractères par blob, ~12 000 au total), pourquoi c'est perdu
(« bruit non-compressible ») et **à quoi ça ressemble quand ça arrive**
(« ralentit la génération à l'extrême et donne l'impression d'un
blocage »).

Le symptôme est la partie la plus utile : c'est ce qui permet de
reconnaître la faute pendant qu'elle se commet, plutôt qu'après. Une
interdiction sans motif ni symptôme serait contournée à la première
hésitation, et l'erreur passerait pour un simple ralentissement.

Pattern *« interdiction-enoncee-avec-son-chiffre »*.

### Axe 4 — `python3` pour le multi-ligne, `sed` pour les scalaires

`SKILL.md:87` le dit sans détour : Python est « plus robuste que sed
pour multi-ligne ». Les fragments éditoriaux font plusieurs lignes et
contiennent des caractères que `sed` traite comme des métacaractères ;
un `sed` de substitution multi-ligne est possible
(`sed -e "/{{X}}/{r fichier" -e "d;}"`) mais illisible et fragile.

Le partage retenu : `sed` pour les valeurs scalaires
(`{{TITLE}}`, `{{YEAR}}`, `{{CONTACT_EMAIL}}`, `{{TITLE_FOR_BANNER}}`),
`python3` avec un dictionnaire `subs` pour les blocs.

### Axe 5 — la portée est l'exécution, pas la maintenance

L'interdiction vise **l'exécution de la skill**. Un contributeur — ou
Claude Code travaillant sur ce dépôt — ouvre légitimement les snippets
pour les modifier. La bonne pratique alors est de **filtrer les blobs à
l'affichage** :

```bash
sed 's/base64,[A-Za-z0-9+/=]\{30,\}/base64,<BLOB>/g' banner-fallback-snippets.html
```

`CLAUDE.md` porte cette distinction, sans laquelle la règle bloquerait
la maintenance qu'elle est censée servir.

## Conditions de légitimité

1. **Aucun blob en contexte, à l'exécution** : le symptôme observable
   est une génération qui s'éternise sur du texte incompréhensible. Un
   seul cas signale que la règle a été perdue de vue.
2. **Blobs intacts** : les data URI du dépôt sont bit-à-bit ceux
   d'origine. Un logo qui ne s'affiche plus dans un bandeau est le
   premier signe d'une altération — d'où le contrôle visuel des deux
   bandeaux en checklist.
3. **Disponibilité d'un shell** : la méthode suppose `awk`, `sed` et
   `python3`. C'est la dépendance la plus dure de la skill ([D-012]) :
   sans shell, aucune stratégie de repli ne respecte la règle.
4. **Écart de coût réel** : la décision suppose les blobs
   significativement plus lourds que les fragments éditoriaux (~12 000
   caractères contre quelques centaines de lignes de contenu). Des logos
   qui deviendraient minuscules affaibliraient l'argument de coût —
   resterait l'argument d'intégrité, qui suffit.
5. **Fragments temporaires non versionnés** : `content_fr.html`,
   `signature.html` et les autres portent du contenu de communication
   réel ; le `.gitignore` les exclut ([D-001], axe 5).

## Conséquences

- La skill a une dépendance **structurelle** à un environnement doté
  d'un système de fichiers et d'un shell : c'est ce qui rend [D-012]
  plus qu'un confort.
- Le remplacement d'un logo est une opération en deux temps : remplacer
  le PNG **et** régénérer les data URI. Le PNG seul ne change rien au
  rendu — piège documenté dans
  `documentation/MAINTENANCE_GABARIT.md`.
- L'extraction des snippets par plages `awk` (`SKILL.md:80-82`) est le
  prix de cette méthode : robuste tant que le fichier de snippets n'est
  pas réordonné.

## Sources

Internes : [D-002] (régime d'antériorité), [D-004] (autonomie du
livrable et rejet du SVG, qui imposent le base64), [D-005] (assemblage
par substitution de placeholders), [D-001] (fragments temporaires
exclus du dépôt), [D-012] (environnement doté d'un shell).
Externes : comportement de Gmail — stripping des `<svg>` inline,
support des data URI (`SKILL.md:158`, `:183`).

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici.

**Note de traçage**, session du 2026-09-09 : la formulation de
`SKILL.md:54` (« donne l'impression d'un blocage ») **suggère** un
épisode vécu, mais aucun artefact ne l'atteste. Elle est donc citée
comme **texte de la règle**, et l'incident supposé n'est pas raconté —
c'est précisément l'anti-pattern écarté par [D-002] (axe 4).
