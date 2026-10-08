---
id: D-010
title: "Cadrage — conventions éditoriales externalisées, tirées de communications réelles"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
  - D-005
patterns:
  - voix-externalisee-hors-regles
  - convention-tiree-du-corpus-observe
  - interdits-nommes-plutot-que-ton-decrit
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefacts :
> `skill/SKILL.md:41` : « Suivre les conventions éditoriales
> d'Epiconcept (voir `references/editorial-patterns.md`). En
> résumé : […] » ;
> `skill/references/editorial-patterns.md:3` : « Tirées de l'analyse de
> 3 communications internes existantes (SAM Environnement, SAM Social,
> Epifun 30 ans). » ;
> `skill/references/editorial-patterns.md:130-136`, section « Mises en
> garde » : « **Pas de "Cher·e·s collègues"** — trop formel pour la
> culture Epiconcept ; **Pas de "Bien cordialement"** […] ; **Pas de
> "Veuillez trouver ci-joint"** […] ; **Pas de bouton "Cliquez ici"**
> générique […] »

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

Une com interne brandée peut être techniquement irréprochable et sonner
faux. Le gabarit règle la forme ([D-005]) ; restait à décider **où vit
la voix** et **d'où elle tire sa légitimité**.

État courant (pré-décision, vérifié) :

- `references/editorial-patterns.md` (136 l.) porte huit sections : ton
  général, structure type d'un mail, table de seize emoji-ancres,
  signature, conventions de sujet, bilingue, footer standard, mises en
  garde.
- `SKILL.md:43-48` n'en donne qu'un **résumé en six puces**, et renvoie
  au fichier de référence.
- Deux sections sont **dupliquées** entre les deux fichiers : les
  conventions de sujet (`SKILL.md:186-198` et
  `editorial-patterns.md:85-102`) et les règles du bilingue
  (`SKILL.md:48` et `editorial-patterns.md:104-115`, cf. [D-011]).
- Le corpus est nommé : trois communications réelles, identifiées par
  leur émetteur.
- Les conventions sont **positives et négatives** : ce qu'on écrit
  (« Bonjour à toutes et à tous, ») et ce qu'on n'écrit pas
  (« Bien cordialement »).
- La table des emoji porte une limite chiffrée : « Ne pas surcharger :
  1 emoji par ancre, 3-5 ancres max dans le corps »
  (`editorial-patterns.md:65`).

**Question centrale** : *« où vivent le ton et les conventions
d'écriture, et sur quoi reposent-ils ? »*

## Décisions actées

### Axe 1 — la voix est un fichier de référence, pas une consigne dans les règles

`references/editorial-patterns.md` est un artefact séparé, à charger
quand on rédige. `SKILL.md` n'en garde qu'un résumé et un renvoi.

Le partage à trois est complet et strict : **la forme dans le template
([D-005]), la procédure dans les règles, la voix dans les
références.** Chacun a un rythme d'évolution propre — la voix bouge
quand la culture interne bouge, la procédure quand l'outillage change,
la forme quand la charte change.

Corollaire de maintenance, énoncé comme un piège dans
`documentation/MAINTENANCE_GABARIT.md` : **le résumé de `SKILL.md` ne
doit pas grossir.** Enrichir les six puces au lieu du fichier de
référence crée deux guides éditoriaux — et le résumé, lu en premier,
gagnerait par accident.

Écarté : tout mettre dans `SKILL.md` — mélange procédure et voix, et
recharge 136 lignes de conventions même quand seule la mise en page est
en jeu. Écarté : ne rien écrire et « suivre le ton Epiconcept » —
inapplicable : le ton n'est pas déductible, c'est précisément ce que le
corpus a servi à établir.

Pattern *« voix-externalisee-hors-regles »*.

### Axe 2 — les conventions sont tirées d'un corpus observé, et le disent

`editorial-patterns.md:3` nomme sa source : trois communications
internes réelles (SAM Environnement, SAM Social, Epifun 30 ans). Les
exemples cités dans le fichier en sont extraits — « Déjà plus de 30
participants : bravo et merci ! 👏 », le « PS : ce message est 100%
ChatGPT-free ».

Deux propriétés en découlent :

- **falsifiabilité** — une règle tirée d'un corpus peut être contredite
  par le corpus. Si les coms réelles cessaient de ressembler à cette
  description, le fichier serait faux et devrait être réécrit, pas
  défendu ;
- **autorité** — les conventions ne sont pas les préférences de
  quelqu'un : elles décrivent ce que l'entreprise écrit déjà. C'est ce
  qui permet d'écrire « trop formel pour la culture Epiconcept » comme
  un constat.

La limite de ce mode d'établissement est assumée : **trois** documents,
d'émetteurs proches (groupes de travail internes). Rien ne garantit
qu'une com de Direction ou de DSI suive les mêmes usages.

Pattern *« convention-tiree-du-corpus-observe »*.

### Axe 3 — les interdits sont nommés, pas déduits d'une description de ton

La section « Mises en garde » liste des **formulations précises** à ne
pas écrire, chacune avec son motif : « Cher·e·s collègues » (trop
formel), « Bien cordialement » / « Bien à vous » (la culture est plus
directe), « Veuillez trouver ci-joint » (administratif), « Cliquez ici »
(lien non explicite), acronymes non explicités.

C'est le mécanisme le plus efficace du fichier. Un ton décrit
(« chaleureux, collectif, factuel ») est interprétable et se dégrade
vers le registre par défaut — qui est précisément le registre
administratif que ces interdits visent. Une liste de formulations
proscrites est **vérifiable** : elle passe en checklist
(`documentation/PREREQUIS_TECHNIQUES.md`).

Le pendant positif suit la même logique : la salutation n'est pas
« chaleureuse », elle est « Bonjour à toutes et à tous, ».

Pattern *« interdits-nommes-plutot-que-ton-decrit »*.

### Axe 4 — les emoji sont un dispositif structurel, plafonné

Les emoji ne sont pas de la décoration : ce sont des **ancres de
section** (`👉` pour le call-to-action, `📅` pour une date, `📍` pour un
lieu, `⏰` pour une deadline…), listées dans une table de seize
entrées, avec leur usage.

Deux règles les encadrent : un emoji par ancre, trois à cinq ancres
maximum dans le corps. Sans plafond, le dispositif se retourne — un
email saturé d'emoji perd la fonction de repérage qui les justifie.

### Axe 5 — la signature dépend de la nature de l'émetteur

Deux formats, et le choix n'est pas libre
(`editorial-patterns.md:83`) : un groupe de travail nomme ses membres
entre parenthèses (« L'équipe SAM ENVIRONNEMENT (Lore, Fabrice, Maud,
Valentin & Yohann) ») ; un groupe « public » (Epifun, DSI, Direction)
donne son nom seul.

C'est ce qui rend la question « émetteur » du cadrage non facultative
([D-006], axe 1) : sans elle, on ne peut pas choisir le format, et une
signature erronée attribue la communication à des personnes qui ne
l'ont pas écrite.

## Conditions de légitimité

1. **Aucune formulation proscrite** dans un livrable : vérifié en
   relecture. C'est la partie automatisable de la conformité éditoriale,
   même si elle reste faite à l'œil aujourd'hui.
2. **Le corpus reste représentatif** : trois documents d'émetteurs
   proches. Si les coms réelles divergent — nouveaux émetteurs, nouvelle
   culture — le fichier de référence est à réécrire **à partir du
   corpus élargi**, pas à défendre.
3. **Le résumé de `SKILL.md` reste un résumé** : s'il se met à contenir
   des règles absentes du fichier de référence, la frontière a cédé.
4. **Duplications resynchronisées** : les conventions de sujet et les
   règles du bilingue existent en deux endroits. Une modification qui
   n'en touche qu'un produit deux vérités
   (`documentation/MAINTENANCE_GABARIT.md`).
5. **Plafond d'emoji tenu** : au-delà de cinq ancres, le dispositif ne
   remplit plus sa fonction.

## Conséquences

- La question « émetteur » du cadrage est structurante, pas
  administrative ([D-006]).
- Le brouillon soumis à validation est **du texte** : c'est à ce stade
  que le ton se corrige, pas après assemblage ([D-006], axe 3).
- Le footer standard est décrit ici **et** écrit en dur dans le template
  ([D-005], axe 5) — d'où la divergence de taille constatée entre les
  deux (`documentation/MAINTENANCE_GABARIT.md`).

## Sources

Internes : [D-002] (régime d'antériorité), [D-005] (frontière forme /
procédure / voix), [D-006] (question « émetteur », validation du
brouillon texte), [D-011] (règles du bilingue, dupliquées ici).
Externes : trois communications internes Epiconcept — SAM
Environnement, SAM Social, Epifun 30 ans — corpus d'établissement des
conventions (`references/editorial-patterns.md:3`).

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici. Les trois communications citées comme corpus ne sont
**pas** dans le dépôt : leur existence est attestée par
`editorial-patterns.md:3`, leur contenu n'a pas été revérifié au
traçage.
