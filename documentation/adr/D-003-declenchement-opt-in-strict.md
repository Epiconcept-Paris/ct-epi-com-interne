---
id: D-003
title: "Cadrage — déclenchement opt-in strict, avec question préalable"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
patterns:
  - opt-in-strict-avec-question-prealable
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefact : `skill/SKILL.md:3`,
> champ `description` de la frontmatter : « ⚠️ NE PAS déclencher
> automatiquement, même pour un email interne Epiconcept. Utiliser
> UNIQUEMENT dans l'un de ces deux cas : (a) l'utilisateur la nomme
> explicitement […] ; (b) Claude estime la skill utile : il ARRÊTE sa
> production, POSE la question en une phrase courte […], ATTEND la
> réponse, puis applique le choix. ⚠️ La question se pose AVANT de
> produire le livrable, jamais après. »

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

« Rédige-moi un mail pour annoncer X à l'équipe » est une demande
banale, et la plus grande partie du temps la bonne réponse est **du
texte**, pas un fichier HTML brandé. Une skill qui se déclencherait sur
ce vocabulaire produirait systématiquement le livrable lourd :

- appliquer le gabarit engage un **workflow en quatre étapes** avec deux
  points d'arrêt utilisateur ([D-006]) : six questions de cadrage, puis
  validation d'un brouillon. Se déclencher seule transforme « écris-moi
  deux paragraphes » en questionnaire ;
- le livrable engage l'**image de l'entreprise** : logo, palette,
  mentions légales, footer « en tant que salarié·e d'Epiconcept ». Ce
  n'est pas un défaut qu'on impose à un brouillon de travail ;
- l'assemblage mobilise le shell et écrit des fichiers ([D-007]) — coût
  disproportionné pour un échange exploratoire ;
- le pire scénario est le double travail : produire un brouillon
  texte/markdown, puis proposer de le « retransformer » en email
  brandé.

État courant (pré-décision, vérifié) :

- `skill/SKILL.md:3` porte l'interdiction, les deux voies d'activation,
  l'ordre « la question AVANT le livrable », l'interdiction explicite du
  retraitement a posteriori, et la conduite par défaut en l'absence
  d'accord (« produire le contenu sans la mise en forme email
  branded »).
- La contrainte est portée par la **frontmatter**, c'est-à-dire par le
  seul texte lu au moment du choix de déclencher — pas par le corps du
  fichier, qui n'est lu qu'après.
- Elle fournit même la phrase à poser : « Tu veux que je formate ça en
  com interne Epiconcept (email HTML branded pour Gmail) ? ».

**Question centrale** : *« à quelles conditions le gabarit email
s'applique-t-il, et qui décide ? »*

## Décisions actées

### Axe 1 — opt-in strict, deux voies seulement

Le déclenchement est **opt-in strict**, énoncé comme une contrainte et
non comme une préférence. Deux voies :

1. **invocation nommée** par l'utilisateur (« /epi-com-interne »,
   « applique epi-com-interne », « com interne Epiconcept ») ;
2. **question préalable** : si Claude juge la skill utile, il **arrête**
   sa production, **pose la question en une phrase courte**, **attend**
   la réponse, puis applique le choix.

En l'absence d'invocation ou de réponse positive, le contenu est produit
**sans** la mise en forme email brandée — pas de refus, pas de
silence : une réponse normale.

Écarté : déclenchement sur le contexte « email interne Epiconcept » —
trop large, et c'est précisément le cas que la `description` nomme pour
l'exclure. Écarté : déclenchement dès qu'un `.html` est demandé — le
format ne dit rien de l'intention.

Pattern *« opt-in-strict-avec-question-prealable »*.

### Axe 2 — la question se pose avant, jamais après

Produire un brouillon texte ou markdown sans la skill puis proposer de
le retransformer est **explicitement interdit**. C'est le mode d'échec
le plus coûteux, et il est spécifique à cette skill : le brouillon
produit hors gabarit n'a pas passé les six questions de cadrage
([D-006]) — il manque l'émetteur, l'email de contact, la décision
bilingue, les choix de bandeaux. Le « retransformer » revient donc à
poser les questions après coup et à réécrire le contenu en fonction des
réponses : le brouillon est perdu, et l'utilisateur a l'impression
d'avoir validé quelque chose qui n'existe plus.

### Axe 3 — la contrainte vit dans la frontmatter, qui est un contrat

L'interdiction est placée là où elle est lue au moment utile, et la
`description` va jusqu'à fournir la formulation de la question.
Corollaire opérationnel : reformuler ce champ « pour le rendre plus
naturel » réactive le déclenchement automatique. Toute modification
passe par un ADR d'amendement.

## Conditions de légitimité

1. **Pas de déclenchement observé sans invocation ni accord** : si des
   emails sortent brandés alors que l'utilisateur n'a rien demandé ni
   confirmé, la formulation de `SKILL.md:3` a dérivé → amendement.
2. **Pas de retraitement a posteriori observé** : un enchaînement
   « brouillon nu, puis proposition de le brander » signale la même
   dérive.
3. **Coût réel de l'application** : la décision suppose que produire
   l'email est un engagement lourd (deux points d'arrêt, assemblage
   shell, livrable brandé). Si le workflow devenait léger, l'argument de
   coût tombe — restent les arguments d'image, qui suffisent, mais
   l'ADR devrait être révisé pour ne plus s'appuyer sur une prémisse
   fausse.
4. **Coût de l'oubli acceptable** : le gabarit est parfois oublié alors
   qu'il aurait été pertinent. Compromis assumé — un oubli se rattrape
   en invoquant la skill, un email brandé à tort se re-fabrique
   entièrement.

## Conséquences

- `skill/SKILL.md:3` est un fichier à modifier avec précaution :
  `CLAUDE.md` le signale comme invariant, et
  `documentation/MAINTENANCE_GABARIT.md` renvoie tout changement de
  déclenchement vers un ADR d'amendement.
- La skill n'est jamais testable « en passant » : toute vérification de
  comportement demande une invocation explicite.

## Sources

Internes : [D-002] (régime d'antériorité), [D-006] (deux points
d'arrêt utilisateur, dont l'absence rend le retraitement destructeur),
[D-007] (coût de l'assemblage), [D-004] (nature brandée du livrable).
Externes : skills `epi-visual` (`ct-epi-visual` D-003) et
`fwk-specs-fonct` (`ct-fwk-specs-fonct` D-003) — même dispositif
d'opt-in strict, formulé dans les mêmes termes ; convergence de
conception au sein des skills Epiconcept.

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici.

**Q1 (traçage à l'identique ?)**, session du 2026-09-09 : la règle de
non-modification du contenu de la skill ([D-001], Q2) a été appliquée →
**règle tracée telle qu'inscrite**, sans reformulation ni durcissement.
