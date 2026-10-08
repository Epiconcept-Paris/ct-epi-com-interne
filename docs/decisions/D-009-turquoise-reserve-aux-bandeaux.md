---
id: D-009
title: "Cadrage — turquoise réservé aux deux bandeaux, logo officiel jamais redessiné"
status: amended
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: [D-015]
sources:
  - D-002
  - D-005
patterns:
  - couleur-affectee-a-une-zone-unique
  - asset-officiel-jamais-reproduit
  - charte-consommee-source-de-verite-ailleurs
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefacts :
> `skill/SKILL.md:135` : « ⚠️ Le turquoise est **réservé aux deux
> bandeaux**. Ne JAMAIS l'utiliser comme fond pour le corps du mail, ni
> pour des blocs intermédiaires. Pas de dégradé turquoise→bleu foncé
> dans les bandeaux non plus — turquoise uniforme. » ;
> `skill/SKILL.md:137` : « Le bleu foncé `#246589` est réservé au
> **texte** (H1 dans le corps), jamais comme fond. » ;
> `skill/SKILL.md:158` : « **Pourquoi HTML/CSS et pas SVG inline ?**
> Gmail strippe ou ignore les `<svg>` inline. »

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

Un gabarit d'email brandé peut dériver de deux façons : par excès de
couleur (des blocs turquoise partout, un dégradé « pour faire moderne »)
ou par redessin des actifs de marque (un cercle et une lettre « e » à la
place du logo). Les deux produisent un email qui « ressemble » à
Epiconcept sans en être.

État courant (pré-décision, vérifié) :

- `SKILL.md:126-137` porte une **table d'affectation** zone → couleur,
  avec une colonne « Pourquoi » : bandeau haut turquoise `#4BCDDB`
  (identité visuelle), corps blanc `#ffffff` (lisibilité), bandeau bas
  **même turquoise** (« Symétrie haut/bas, bookend visuel »), footer
  blanc à texte 10px `#bbbbbb` (discrétion).
- `SKILL.md:139-157` détaille les deux bandeaux : hauteurs
  (~140-160px en haut, ~100-120px en bas), tailles de logo (56×56 puis
  44×44), libellés (« COMMUNICATION INTERNE » en haut, « Epiconcept » +
  « SMART HEALTH » en bas).
- `SKILL.md:160-166` fixe l'usage des logos : `logo-e-white.png`
  (80×80, silhouette blanche) est utilisée dans les **deux** bandeaux,
  puisque les deux ont un fond turquoise ; `logo-e-turquoise.png` est
  déclarée « plus utilisée », gardée en réserve.
- Les deux couleurs sont **celles de la charte Epiconcept**, définies
  dans `ct-epi-visual/skill/SKILL.md` (§Palette) : `#246589` bleu foncé,
  `#4BCDDB` bleu clair / turquoise. Rien dans cette skill ne le
  mentionne.

**Question centrale** : *« où la couleur de marque a-t-elle le droit
d'apparaître, et d'où vient le logo ? »*

## Décisions actées

### Axe 1 — une couleur, une zone : le turquoise appartient aux bandeaux

`#4BCDDB` en **fond des deux bandeaux, et nulle part ailleurs**. Corps
du mail blanc. Pas de bloc intermédiaire coloré, pas de dégradé.

L'affectation par zone est ce qui rend la règle vérifiable : on ne
discute pas « est-ce trop de turquoise ? », on regarde si du turquoise
apparaît hors des deux bandeaux. Le motif de fond est la **lisibilité**
(un corps de texte sur turquoise se lit mal, et un email n'a pas de
contrôle de contraste) et la **rareté** (une couleur d'identité perd son
effet à mesure qu'elle se répand).

Le refus du dégradé est explicite et va dans le même sens : un dégradé
turquoise → bleu foncé rendrait le bandeau dépendant du support (Gmail
rend mal les gradients CSS) et ferait du bleu foncé un fond, ce que
l'axe 2 interdit.

Pattern *« couleur-affectee-a-une-zone-unique »*.

### Axe 2 — le bleu foncé est une couleur de texte

`#246589` en **texte** uniquement — le H1 dans le corps. Jamais en fond.

La règle est symétrique de la précédente et complète la répartition :
turquoise = surface, bleu foncé = signe. Un bandeau bleu foncé donnerait
deux identités visuelles concurrentes dans le même email.

### Axe 3 — la symétrie haut/bas est un choix de composition

Les deux bandeaux partagent le **même** turquoise ; le bas est
simplement plus compact (padding 28px contre 40px, logo 44px contre
56px). C'est le « bookend visuel » de `SKILL.md:132` : le contenu est
encadré, l'email a un début et une fin nets.

C'est aussi ce qui explique que le **même** logo blanc serve en haut et
en bas ([D-005], axe 3, pour la position des blocs).

### Axe 4 — le logo est l'image officielle, jamais une reproduction

Le logo « e » est un PNG embarqué, dérivé de l'actif officiel porté par
`ct-epi-visual` (`LOGO_e_turquoise.png`, assorti dans ce dépôt de
l'interdiction explicite de le reproduire à la main). Il n'est ni
redessiné en formes, ni approximé par un cercle et une lettre.

Deux raisons se cumulent ici :

- **marque** — un logo redessiné est un logo faux, même s'il ressemble ;
- **technique** — Gmail strippe les `<svg>` inline
  (`SKILL.md:158`), donc la seule option universelle est un PNG en data
  URI ([D-004], [D-007]). Le redessin en HTML/CSS n'est même pas
  disponible.

`logo-e-white.png` est une **variante dérivée** (silhouette blanche
80×80) qui n'existe pas dans `ct-epi-visual` : si le logo officiel
change, cette variante doit être regénérée, puis ré-encodée en base64
dans les deux snippets.

Pattern *« asset-officiel-jamais-reproduit »*.

### Axe 5 — la charte est consommée ici, elle fait autorité ailleurs

Les valeurs `#4BCDDB` et `#246589` sont **recopiées** de la charte
Epiconcept. Ce dépôt en est consommateur ; la source de vérité est
`ct-epi-visual`.

La dépendance n'est **pas déclarée** dans `skill/SKILL.md` — état de
fait constaté au traçage, non corrigé ([D-001], Q2). Conséquence à
connaître : une évolution de charte ne se propage pas ici
automatiquement, c'est une action de maintenance manuelle. En cas de
contradiction, la charte prévaut ; corriger ici sans corriger là-bas
crée deux vérités.

Pattern *« charte-consommee-source-de-verite-ailleurs »*.

## Conditions de légitimité

1. **Aucun turquoise hors bandeaux**, aucun bleu foncé en fond : vérifié
   en relecture du fichier produit
   (`documentation/PREREQUIS_TECHNIQUES.md`).
2. **Symétrie des bandeaux préservée** : deux fonds identiques. Un écart
   de teinte entre haut et bas signale un snippet modifié à moitié.
3. **Logo non redessiné** : le bandeau affiche l'image, pas une
   approximation. Un logo qui ne s'affiche plus (blob altéré, cf.
   [D-007]) doit être **réparé**, jamais remplacé par un substitut
   dessiné.
4. **Valeurs alignées sur la charte** : à revérifier dans
   `ct-epi-visual` avant toute modification de couleur. Une divergence
   constatée se résout **du côté de la charte**.
5. **Variante blanche regénérable** : `logo-e-white.png` dérive de
   l'actif officiel. Si le procédé de dérivation est perdu, la variante
   devient un actif orphelin — à documenter si le logo évolue.

## Conséquences

- Les couleurs et les dimensions figurent à la fois dans `SKILL.md`
  (règles et « détail des bandeaux ») et dans les fichiers HTML :
  duplication volontaire, à resynchroniser à la main
  (`documentation/MAINTENANCE_GABARIT.md`).
- Remplacer un logo est une opération en deux temps — le PNG **et** les
  data URI ([D-007]) — et une troisième si l'inventaire de
  `SKILL.md:200-207` doit suivre.
- L'inventaire final de `SKILL.md:205-206` contredit `:164-166` sur
  l'usage des deux variantes : divergence connue, non corrigée
  (`documentation/MAINTENANCE_GABARIT.md`).

## Sources

Internes : [D-002] (régime d'antériorité), [D-004] (data URI et rejet
du SVG), [D-005] (position et symétrie des bandeaux), [D-007]
(intégrité des blobs), [D-001] (contenu de la skill laissé inchangé).
Externes : `Epiconcept-Paris/ct-epi-visual` — palette officielle
(§Palette de `skill/SKILL.md`) et logo « e » officiel, avec
l'interdiction de reproduction manuelle (`ct-epi-visual` D-005).

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici.

**Note de traçage**, session du 2026-09-09 : l'axe 5 (dépendance à la
charte) **n'est pas écrit** dans la skill — il est établi en comparant
les valeurs hexadécimales avec `ct-epi-visual/skill/SKILL.md:18-19`. Il
est consigné ici comme constat vérifiable, et signalé comme tel plutôt
que présenté comme une règle du gabarit.
