---
id: D-005
title: "Cadrage — gabarit à placeholders et snippets externalisés, structure verticale imposée"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
  - D-004
patterns:
  - gabarit-externalise-forme-hors-regles
  - placeholder-comme-contrat-d-assemblage
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefacts :
> `skill/SKILL.md:12-20` : « Un fichier `.html` unique, self-contained,
> avec cette structure verticale (de haut en bas) : 1. Preheader […]
> 7. Footer » ;
> `skill/assets/template.html:1-16` : en-tête de commentaires listant
> les onze placeholders à substituer ;
> `skill/assets/banner-fallback-snippets.html:23-83` : quatre snippets
> délimités par des bandes de commentaires (bandeau haut, bandeau bas,
> variantes à image, divider FR/EN).

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

Le HTML d'email est verbeux : tables imbriquées, styles inline répétés,
attributs de compatibilité ([D-004]). Le produire par génération libre à
chaque exécution donnerait un markup légèrement différent à chaque fois
— donc un rendu à revérifier de zéro à chaque email, sans validateur
pour le faire.

État courant (pré-décision, vérifié) :

- `template.html` (107 l.) porte la structure complète : wrapper
  pleine largeur, conteneur 600px, sept `<tr>` correspondant aux sept
  blocs, footer légal en dur (copyright, mention salarié·e, adresse,
  contact).
- Les seuls trous sont **onze placeholders** `{{…}}`, documentés dans
  l'en-tête du fichier lui-même ; les snippets en ajoutent trois qui leur
  sont propres (`{{TITLE_FOR_BANNER}}`, `{{IMG_URL}}`, `{{ALT_TEXT}}`).
- `banner-fallback-snippets.html` sort les bandeaux du template :
  ils varient (fallback brandé ou image fournie) alors que le reste ne
  varie pas.
- Trois placeholders sont **conditionnels** : `{{LANG_NOTICE}}`,
  `{{LANG_DIVIDER}}` et `{{CONTENT_EN}}`, vides en mono-langue
  ([D-011]).
- Le footer légal, lui, n'est **pas** un placeholder : son texte est
  écrit dans le template (`template.html:89-91`).

**Question centrale** : *« la forme de l'email est-elle un fichier à
substituer ou un markup à regénérer, et l'auteur peut-il en changer
l'ordre ? »*

## Décisions actées

### Axe 1 — la forme est un fichier à substituer, pas un markup à regénérer

Le template est un **artefact séparé** dont on remplace les
placeholders. Il n'est ni décrit en prose dans les règles, ni
reconstruit à l'exécution.

Le bénéfice est la **stabilité du rendu** : le markup qui passe dans
Gmail a été vérifié une fois, et il est réutilisé à l'identique. Comme
il n'existe aucun validateur d'email ([D-004]), c'est le seul mécanisme
de garantie disponible — un markup regénéré serait à revalider à l'œil,
dans Gmail, à chaque email.

Corollaire de maintenance : **la forme dans le template, la procédure
dans les règles, la voix dans les références** ([D-010]). Recopier une
valeur de padding dans `SKILL.md` crée une source de vérité concurrente
qui divergera — ce qui est déjà arrivé pour la taille du footer
(divergence connue, cf. `documentation/MAINTENANCE_GABARIT.md`).

L'exception assumée est la liste des sept blocs (`SKILL.md:12-20`) et
le rappel des dimensions de bandeaux (`SKILL.md:139-157`) : duplications
volontaires, lues *avant* l'ouverture des fichiers HTML, qui servent de
garde-fou et de référence de dépannage.

Écarté : template inline dans `SKILL.md` — des dizaines de lignes de
markup dans un fichier de règles, illisible et rechargé à chaque
exécution. Écarté : génération du markup à la demande — détruit la
stabilité du rendu, qui est l'objet même de la décision.

Pattern *« gabarit-externalise-forme-hors-regles »*.

### Axe 2 — les placeholders sont un contrat, documenté dans le fichier

Les onze `{{…}}` du template forment l'interface entre le contenu et la
forme, et leur liste vit dans **l'en-tête de commentaires du template**
(`template.html:1-16`) — au plus près de leurs occurrences, donc
difficile à oublier lors d'une modification.

Le mode d'échec impose cette discipline : rien ne vérifie qu'un
placeholder a été substitué. Un `{{…}}` renommé dans le template mais
pas dans le dictionnaire d'assemblage ne provoque **aucune erreur** — il
s'affiche en clair dans l'email envoyé. Un placeholder est donc un
incrément **MAJEUR** au sens du `CHANGELOG.md`.

Pattern *« placeholder-comme-contrat-d-assemblage »*.

### Axe 3 — la structure verticale est imposée, dans cet ordre

Sept blocs, de haut en bas : preheader → bandeau haut → titre (+ mention
bilingue) → contenu FR → *(divider + contenu EN)* → signature → bandeau
bas → footer légal.

L'ordre porte du sens, pas seulement de la mise en page :

- les deux bandeaux encadrent le contenu — c'est le **bookend visuel**
  revendiqué par `SKILL.md:132`, qui exige la symétrie haut/bas et fonde
  la règle de couleur de [D-009] ;
- le footer légal est **après** le bandeau bas, en petit et discret :
  c'est de la mention obligatoire, pas du contenu ;
- la signature est **avant** le bandeau bas : elle appartient au message,
  pas à l'habillage.

Un lecteur habitué aux coms internes Epiconcept reconnaît cette
séquence. C'est ce que la stabilité de structure achète.

### Axe 4 — les bandeaux sont externalisés parce qu'ils varient

Ce qui varie sort du template. Les bandeaux existent en deux régimes —
fallback brandé (turquoise + logo + titre) ou image fournie par
l'utilisateur — et se substituent par des blocs entiers
(`{{HEADER_BANNER}}`, `{{FOOTER_BANNER}}`), pas par des valeurs.

Le divider FR/EN est logé au même endroit pour la même raison : présent
ou absent selon [D-011].

Conséquence de découpage : les snippets sont extraits par plages de
lignes en `awk` (`SKILL.md:80-82`), avec une garde `NR>30` codée en
dur — fragilité réelle, documentée dans
`documentation/PREREQUIS_TECHNIQUES.md`, à connaître avant de
réordonner le fichier.

### Axe 5 — le footer légal est en dur, volontairement

Copyright, mention « en tant que salarié·e d'Epiconcept », adresse
postale : écrits dans le template, pas paramétrés. Ce sont des mentions
d'entreprise, identiques pour toute com interne ; les exposer en
placeholders inviterait à les modifier au cas par cas, ce qui est
exactement ce qu'il faut éviter d'un email à l'autre.

Seuls l'année (`{{YEAR}}`) et l'email de contact (`{{CONTACT_EMAIL}}`)
sont variables.

## Conditions de légitimité

1. **Aucun `{{…}}` résiduel** dans un livrable : contrôlé en relecture
   (checklist de `documentation/PREREQUIS_TECHNIQUES.md`). C'est la
   vérification la plus rentable du gabarit.
2. **Placeholders conditionnels vraiment vides** en mono-langue : ni
   laissés en placeholder, ni remplis d'un texte de remplissage.
3. **Structure effectivement identique** d'un email à l'autre : deux
   coms produites avec la même version du gabarit ont les mêmes sept
   blocs dans le même ordre.
4. **En-tête du template à jour** : la liste des placeholders y est
   exhaustive. Un placeholder non documenté est un piège pour le
   mainteneur suivant.
5. **Extraction des snippets robuste** : après toute modification de
   `banner-fallback-snippets.html`, un email de test est produit et les
   deux bandeaux vérifiés à l'œil — la garde `NR>30` ne survit pas à un
   réordonnancement.

## Conséquences

- Toute modification de structure touche **plusieurs fichiers** dans le
  même commit : `template.html` pour la forme, `SKILL.md:12-20` pour la
  liste des blocs, l'en-tête du template si un placeholder change.
- `documentation/MAINTENANCE_GABARIT.md` porte la carte de ces
  duplications et la règle de resynchronisation.
- La conversion a posteriori d'un brouillon libre vers le gabarit est
  destructrice — ce qui renforce l'interdiction du retraitement posée
  par [D-003] (axe 2).

## Sources

Internes : [D-002] (régime d'antériorité), [D-004] (contraintes Gmail
qui dictent le markup, et absence de validateur qui rend la stabilité
nécessaire), [D-007] (assemblage par substitution), [D-010] (frontière
avec le guide éditorial), [D-011] (placeholders conditionnels du
bilingue).

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici.
