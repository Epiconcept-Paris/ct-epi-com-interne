---
id: D-011
title: "Cadrage — bilingue FR/EN dans un seul email, par adaptation et non par traduction"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
  - D-005
  - D-010
patterns:
  - deux-langues-un-seul-envoi
  - adaptation-plutot-que-traduction-litterale
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefacts :
> `skill/SKILL.md:48` : « **Si bilingue** : version EN après FR, séparée
> par un divider horizontal + marqueur `🇬🇧 english version below`. L'EN
> est une **adaptation** (pas une traduction littérale) — on garde le
> ton, on adapte les références culturelles si nécessaire » ;
> `skill/references/editorial-patterns.md:104-115` : « Quand le mail
> s'adresse aussi aux équipes internationales (Océan Indien, partenaires
> anglophones) : 1. Mettre `🇬🇧 english version below` en petit juste
> sous le titre principal (FR) […] 5. La version EN est une
> **adaptation**, pas une traduction littérale […] On garde le même
> nombre de sections et la même structure ».

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

Epiconcept a des équipes non francophones — l'Océan Indien est nommé
comme destinataire (`editorial-patterns.md:106`). Une com interne qui
les concerne doit leur parvenir, et deux stratégies s'excluaient :
deux envois séparés, ou un seul email portant les deux langues.

État courant (pré-décision, vérifié) :

- Le bilingue est l'une des **six questions de cadrage**
  (`SKILL.md:33`), donc une décision prise **avant** la rédaction
  ([D-006]).
- Il active trois placeholders conditionnels du gabarit
  ([D-005]) : `{{LANG_NOTICE}}` (le marqueur sous le titre),
  `{{LANG_DIVIDER}}` (le trait entre les deux versions) et
  `{{CONTENT_EN}}`. Vides en mono-langue.
- Le divider est un **snippet dédié** de
  `banner-fallback-snippets.html` (bloc « DIVIDER FR / EN »,
  `:77-83`).
- `SKILL.md:33` précise l'ordre : « FR puis EN, séparées par un trait
  + 🇬🇧 ».
- `editorial-patterns.md:111` demande que la version EN ait **son propre
  titre en anglais**.
- Les conventions de sujet prévoient le cas : `REMINDER` en anglais à
  côté de `RAPPEL` (`SKILL.md:192`), et l'un des trois exemples cités
  est un sujet anglais (`SKILL.md:198`).

**Question centrale** : *« comment une com interne atteint-elle les
équipes non francophones ? »*

## Décisions actées

### Axe 1 — un seul email, deux langues, FR d'abord

Les deux versions cohabitent dans le même fichier, français en premier,
anglais après un divider.

Un seul envoi, pour trois raisons :

- **une seule liste de diffusion** — deux envois supposeraient de savoir
  qui lit quoi ; l'entreprise n'a pas cette information, et une
  personne mal classée ne reçoit rien ;
- **une seule version de vérité** — deux emails séparés divergent dès la
  première correction. Ici, la correction touche un fichier ;
- **pas de hiérarchie de traitement** — tout le monde reçoit le même
  message, avec la même mise en page, le même bandeau, le même footer.

Le FR en premier est un choix, pas un ordre alphabétique : c'est la
langue de travail majoritaire. La contrepartie est explicitement traitée
par le marqueur de l'axe 2.

Écarté : deux emails distincts — dépend d'un ciblage indisponible, et
double la maintenance. Écarté : EN seul quand des non-francophones sont
concernés — dégrade le message pour la majorité. Écarté : deux colonnes
côte à côte — ingérable sur 600px et en mobile ([D-008]).

Pattern *« deux-langues-un-seul-envoi »*.

### Axe 2 — le marqueur `🇬🇧 english version below`, en haut

Un lecteur anglophone ouvre un email qui commence en français. Sans
signal, il le ferme. Le marqueur est donc placé **sous le titre
principal**, avant le corps FR — pas au-dessus de la version EN, où il
serait lu trop tard.

C'est la fonction du placeholder `{{LANG_NOTICE}}`, positionné dans le
template juste après le H1 (`template.html:55`), en 13px gris. Discret
pour le lecteur francophone, suffisant pour l'autre.

### Axe 3 — l'EN est une adaptation, avec la même structure

La version anglaise **n'est pas une traduction littérale** : on garde le
ton, on adapte les références culturelles, on lui donne son propre titre
en anglais.

Le motif est la cohérence avec [D-010] : le ton chaleureux et collectif
des coms Epiconcept est porté par des formulations françaises
(« Bonjour à toutes et à tous, », « À très vite, ») dont la traduction
mot à mot sonne étrange ou plate. Traduire littéralement produirait une
version EN correcte et sans voix — c'est-à-dire le registre
administratif que les interdits de [D-010] visent.

La contrainte qui borde cette liberté est structurelle : **même nombre
de sections, même structure** (`editorial-patterns.md:113`). L'adaptation
porte sur la langue et les références, jamais sur le contenu. Sans cette
borne, « adaptation » autoriserait à raccourcir la version EN — et à
donner moins d'information aux équipes qui en ont déjà le moins.

Pattern *« adaptation-plutot-que-traduction-litterale »*.

### Axe 4 — le bilingue est décidé au cadrage, jamais après

C'est une question du cadrage ([D-006], axe 1) parce que c'est une
**décision structurelle** : elle double le volume rédactionnel, active
trois placeholders et change la ligne de sujet (`REMINDER` plutôt que
`RAPPEL`). L'ajouter après validation du brouillon impose de reprendre
la rédaction ; le retirer laisse des placeholders à vider.

En mono-langue, les trois placeholders sont **vides** — ni laissés en
`{{…}}`, ni remplis d'un texte de remplissage ([D-005], condition 2).

## Conditions de légitimité

1. **Marqueur présent quand l'email est bilingue**, et absent sinon :
   un `🇬🇧 english version below` dans un email mono-langue promet une
   version qui n'existe pas.
2. **Parité de structure** : la version EN a le même nombre de sections
   que la FR. Une version EN sensiblement plus courte signale une
   traduction abrégée, pas une adaptation.
3. **Placeholders conditionnels vides en mono-langue** : vérifié en
   relecture (`documentation/PREREQUIS_TECHNIQUES.md`).
4. **Le français reste la langue de travail majoritaire** : c'est la
   prémisse de l'ordre FR → EN. Si l'équilibre changeait, l'ordre
   devrait être rouvert.
5. **Le ciblage reste indisponible** : la décision d'un envoi unique
   suppose qu'on ne sait pas qui lit quoi. Si des listes par langue
   existaient un jour, l'arbitrage change.

## Conséquences

- Trois des onze placeholders du gabarit n'existent que pour ce cas
  ([D-005]), et le divider est un snippet dédié.
- La ligne de sujet doit couvrir les deux publics — d'où les variantes
  `RAPPEL` / `REMINDER` des conventions de sujet ([D-010]).
- Le volume double, donc le budget de poids de 102 Ko se consomme plus
  vite ([D-008], axe 2) — sans risque réel pour du texte, mais à savoir.

## Sources

Internes : [D-002] (régime d'antériorité), [D-005] (placeholders
conditionnels et snippet divider), [D-006] (décision prise au cadrage),
[D-008] (contraintes de largeur et de poids), [D-010] (ton dont
l'adaptation doit préserver la voix).
Externes : équipes Epiconcept non francophones — Océan Indien et
partenaires anglophones, nommés dans
`references/editorial-patterns.md:106`.

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici.
