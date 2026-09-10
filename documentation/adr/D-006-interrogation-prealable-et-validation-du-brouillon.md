---
id: D-006
title: "Cadrage — interrogation préalable puis validation du brouillon texte : deux points d'arrêt"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
  - D-005
patterns:
  - interrogation-prealable-groupee
  - validation-au-stade-le-moins-couteux
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefacts :
> `skill/SKILL.md:26` : « ⚠️ **Avant toute rédaction**, poser ces
> questions à l'utilisateur via l'outil `ask_user_input_v0` (regrouper
> en un seul appel quand c'est possible) » — suivi d'un tableau de six
> questions, chacune avec sa colonne « Pourquoi » ;
> `skill/SKILL.md:50` : « Présenter d'abord le **brouillon texte** à
> l'utilisateur pour validation avant de générer le HTML final. C'est un
> point de contrôle important : la mise en page coûte cher à refaire, le
> texte est plus rapide à itérer. »

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

Une com interne part à toute l'entreprise, une seule fois, sans
possibilité de correction après envoi. Et six informations lui sont
indispensables que le prompt initial ne contient presque jamais toutes.

État courant (pré-décision, vérifié) :

- `SKILL.md:28-36` : les six questions sont **sujet/titre court**,
  **émetteur**, **email de contact**, **bilingue FR/EN ?**, **bandeau du
  haut**, **bandeau du bas**. Chacune est justifiée par l'usage qu'en
  fait le gabarit (« Sert au `<title>`, au preheader subject, et au
  H1 »).
- `SKILL.md:26` demande de **regrouper** les questions en un seul appel.
- `SKILL.md:37` interdit de redemander ce qui est déjà fourni : « Si
  l'utilisateur fournit déjà tous ces éléments dans le prompt initial,
  ne **pas** redemander — utiliser ce qui est là. »
- `SKILL.md:50` place un second arrêt, avec son motif de coût.
- Le workflow est explicitement séquencé en quatre étapes
  (`SKILL.md:24`, `:39`, `:52`, `:118`).

**Question centrale** : *« que fait la skill de ce qu'elle ne sait pas,
et à quel moment fait-elle valider ? »*

## Décisions actées

### Axe 1 — six questions, groupées, avant toute rédaction

La phase de cadrage précède la rédaction. Les six informations ne sont
pas facultatives : chacune alimente un placeholder ou une branche du
gabarit ([D-005]).

Trois d'entre elles ne sont **pas devinables**, et c'est ce qui rend la
question obligatoire plutôt que polie :

- l'**émetteur** — « Equipe SAM Environnement (Lore, Fabrice, Maud) »
  ou « Epifun » : la convention de signature diffère selon qu'il s'agit
  d'un groupe de travail (membres nommés) ou d'un groupe public (nom
  seul), cf. [D-010] ;
- l'**email de contact** — il devient un `mailto:` cliquable dans la
  signature **et** dans le footer. Inventer une adresse produit un lien
  mort envoyé à toute l'entreprise ;
- le **bilingue** — décision structurelle : il double le contenu et
  active trois placeholders ([D-011]). Se tromper impose de tout
  reprendre.

Le **regroupement** en un seul appel est une règle d'ergonomie, pas de
performance : six questions posées une par une transforment le cadrage
en interrogatoire et donnent l'impression que la skill improvise.

Écarté : rédiger avec des `[À COMPLÉTER]` — reporte le travail sur
l'utilisateur, et un marqueur oublié part dans l'email. Écarté :
déduire l'émetteur du contexte de la conversation — plausible, donc
dangereux : une signature erronée attribue une communication à des
personnes qui ne l'ont pas écrite.

Pattern *« interrogation-prealable-groupee »*.

### Axe 2 — ne jamais redemander ce qui est déjà fourni

`SKILL.md:37` est le contrepoids de l'axe 1 : la phase de cadrage sert à
**combler des manques**, pas à faire répéter. Un utilisateur qui a
détaillé son besoin en dix lignes et qui reçoit malgré tout les six
questions conclut que la skill n'a pas lu son message.

C'est aussi ce qui rend la règle tenable : bien utilisée, la phase de
cadrage est souvent vide.

### Axe 3 — la validation se fait sur le **texte**, avant la mise en page

Second point d'arrêt : le brouillon texte est soumis avant tout
assemblage HTML. Le motif est écrit dans la skill — « la mise en page
coûte cher à refaire, le texte est plus rapide à itérer ».

Il est plus fort qu'un simple coût de calcul. Corriger le fond après
assemblage impose de refaire les fragments, de relancer l'extraction des
snippets et la substitution ([D-007]), puis de **revérifier le rendu
dans Gmail** — le seul contrôle disponible étant visuel ([D-004]). Un
aller-retour sur le texte coûte un message ; un aller-retour sur
l'email coûte un cycle complet.

Corollaire : l'utilisateur valide ce qu'il comprend. Un HTML de 600px en
tables imbriquées n'est pas relisable ; un brouillon texte l'est. Faire
valider l'email plutôt que le texte revient à faire approuver une forme
en espérant que le fond suivra.

Pattern *« validation-au-stade-le-moins-couteux »*.

### Axe 4 — quatre étapes, dans l'ordre, non fusionnables

Cadrage → rédaction → assemblage → livraison. Les deux arrêts tombent
entre 1 et 2, puis entre 2 et 3. Fusionner les étapes fait perdre
exactement ce que chaque arrêt protège : le cadrage évite d'écrire un
contenu qu'il faudra reprendre, la validation évite d'assembler un
email qu'il faudra refaire.

## Conditions de légitimité

1. **Aucune information inventée** : émetteur, email de contact et
   décision bilingue viennent de l'utilisateur, jamais d'une déduction.
   Un `mailto:` mort dans un email diffusé invalide la règle en
   pratique.
2. **Cadrage effectivement tenu quand il manque quelque chose**, et
   **effectivement omis quand tout est fourni** : les deux dérives sont
   symétriques et toutes deux observables.
3. **Second arrêt effectivement tenu** : si des emails sont assemblés
   sans que le texte ait été validé, la règle de `SKILL.md:50` a dérivé.
4. **Coût des questions supportable** : la décision suppose que
   l'utilisateur préfère six questions groupées à un email à refaire. Si
   le cadrage devenait dissuasif, c'est son **périmètre** qu'il faudrait
   réduire, pas l'interdiction d'inventer qu'il faudrait relâcher.
5. **Disponibilité de l'outil de question** : `ask_user_input_v0` est
   un outil d'environnement ([D-012]). Sans lui, les questions se
   posent en conversation — la règle ne change pas, seul son véhicule.

## Conséquences

- La skill n'est pas utilisable en « one-shot » silencieux : elle
  produit au minimum un échange avant le livrable. C'est un motif
  supplémentaire de l'opt-in strict ([D-003]).
- L'interdiction du retraitement a posteriori ([D-003], axe 2) tient à
  cette décision : un brouillon produit hors gabarit n'a pas passé le
  cadrage, donc « le rebrander » revient à poser les questions après
  coup et à réécrire le contenu.

## Sources

Internes : [D-002] (régime d'antériorité), [D-003] (opt-in strict, dont
les deux arrêts sont un motif), [D-004] (contrôle de rendu uniquement
visuel, qui renchérit le cycle post-assemblage), [D-005] (placeholders
alimentés par les six réponses), [D-010] (conventions de signature
dépendant de l'émetteur), [D-011] (bilingue comme décision
structurelle), [D-012] (outil `ask_user_input_v0`).

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici. Le motif de coût cité à l'axe 3 est **le texte de la
règle** (`SKILL.md:50`), non le compte-rendu d'un incident.
