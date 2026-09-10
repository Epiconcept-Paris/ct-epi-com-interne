---
id: D-008
title: "Cadrage — les contraintes Gmail comme cadre technique du markup"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
  - D-004
patterns:
  - contrainte-documentee-avec-sa-cause
  - budget-mesurable-plutot-que-consigne-de-sobriete
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefact : `skill/SKILL.md:174`
> et la liste `:176-184` : « Cible primaire : **Gmail (Google Workspace
> Epiconcept)** — web, iOS, Android. Quelques règles à respecter dans
> l'HTML produit : […] **Tables pour le layout** (pas de flexbox/grid
> CSS) — Gmail strippe certains styles modernes ; **Styles inline
> uniquement** […] ; **Largeur maximale** : 600px […] ; **Poids total
> < 102 Ko** : au-delà, Gmail tronque le message et affiche
> « [Message clipped] » […] »

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

[D-004] a fixé la cible : Gmail, un fichier autonome collé dans un
message. Restait à décider **comment ces contraintes vivent dans la
skill** — comme une consigne générale de prudence, ou comme un cadre
explicite, chiffré et motivé.

État courant (pré-décision, vérifié) :

- Huit contraintes sont listées, **chacune avec sa cause** : tables
  (Gmail strippe les styles modernes), styles inline (Gmail strippe
  `<style>` en `<head>` quand on colle l'HTML), 600px (« Gmail web rend
  correctement à cette largeur »), < 102 Ko (troncature et
  « [Message clipped] »), police avec repli Arial (Source Sans Pro n'est
  pas web-safe email), hex 6 caractères (Gmail ignore `rgb()` et les
  variables CSS), `width`/`height` en attributs HTML (Gmail tronque les
  images sans dimensions explicites), pas de JS / `<form>` / `<iframe>`
  (Gmail les strippe ou refuse l'envoi).
- Deux règles sont **des autorisations**, pas des interdictions : les
  data URI sont explicitement déclarés OK (`:183`), et le repli sur
  Arial est déclaré « rendu attendu, propre » (`:180`).
- `SKILL.md:158` porte la même logique sur le SVG, dans une section
  séparée, avec sa cause et sa solution de rechange.
- `template.html` et les snippets appliquent les huit règles.

**Question centrale** : *« sous quelle forme les limites du client de
messagerie entrent-elles dans le gabarit ? »*

## Décisions actées

### Axe 1 — chaque contrainte est documentée avec sa cause

Aucune règle n'est énoncée seule. « Tables pour le layout » sans
justification se lit comme un archaïsme de développeur email et se
contourne dès qu'on connaît flexbox ; « Tables pour le layout — Gmail
strippe certains styles modernes » est non négociable tant que la cause
tient.

Le bénéfice est la **révisabilité** : chaque règle porte la condition de
sa propre péremption. Si Gmail se mettait un jour à supporter `<style>`
en `<head>` au collage, la règle « styles inline uniquement » tomberait
d'elle-même, et on saurait laquelle rouvrir. Un cadre non motivé se
transmet par superstition et ne se révise jamais.

Écarté : consigne générale (« écrire du HTML d'email compatible ») —
inapplicable, et laisse chaque exécution réarbitrer. Écarté : viser
« tous les clients » — cumulerait les contournements d'Outlook (VML,
conditional comments) sans que personne ne puisse dire lesquels servent
encore ([D-004], axe 2).

Pattern *« contrainte-documentee-avec-sa-cause »*.

### Axe 2 — le poids est un budget mesurable, pas une recommandation de sobriété

< 102 Ko, avec la conséquence nommée : « [Message clipped] ». C'est la
seule contrainte du gabarit qui soit **vérifiable par une commande**
(`wc -c`), et la seule dont le dépassement dégrade l'email **après
l'envoi**, côté destinataire, sans que l'expéditeur s'en aperçoive.

Le budget est déjà partiellement consommé : ~12 Ko par les deux logos
en base64 ([D-007]). Ce qui reste est confortable pour du texte, et
s'épuise vite si on ajoute des images embarquées — d'où l'avertissement
explicite de `SKILL.md:179` (« vérifier si on ajoute beaucoup
d'images »).

C'est aussi ce qui rend la troncature dangereuse : Gmail coupe **la
fin** du message, donc la signature, le bandeau bas et le footer légal
([D-005], axe 3) — exactement les blocs obligatoires.

Pattern *« budget-mesurable-plutot-que-consigne-de-sobriete »*.

### Axe 3 — certaines limites sont acceptées comme rendu attendu

Deux cas sont assumés plutôt que contournés :

- **la police** — Gmail servira Arial. Écrit noir sur blanc comme le
  rendu attendu (`:180`). Ne pas tenter d'embarquer la police : Gmail
  strippe `@font-face`, et un `@font-face` en base64 ferait exploser le
  budget des 102 Ko pour un gain nul ;
- **les images de bandeau fournies par l'utilisateur** — leur URL est
  externe, donc bloquable par le client. Le gabarit exige alors 600px
  minimum et un `alt` descriptif (`:168`) : on ne corrige pas le
  blocage, on rend l'email lisible quand il survient.

Nommer un écart comme attendu évite qu'un mainteneur le prenne pour un
bug et « corrige » vers une solution que Gmail refuse.

### Axe 4 — pas de JS, pas de formulaire, pas d'iframe : contrainte et propriété

Ces trois interdits sont posés comme des contraintes Gmail (`:184`).
Ils ont un effet de bord souhaitable, à ne pas perdre en cas
d'évolution : l'email produit est **inerte** — pas de pixel de suivi,
pas de collecte, rien qui s'exécute chez le destinataire.

Une com interne n'a pas à mesurer les ouvertures de ses collègues. Si
Gmail assouplissait ces règles, l'argument technique tomberait mais
l'argument de sobriété resterait — et devrait alors être tracé pour
lui-même.

## Conditions de légitimité

1. **Les causes citées restent vraies** : le comportement de Gmail est
   hors de notre contrôle. Chaque règle est à revérifier quand un
   rendu inattendu apparaît, avant d'inventer un contournement.
2. **Seuil de 102 Ko mesuré, pas supposé** : contrôle en checklist
   (`documentation/PREREQUIS_TECHNIQUES.md`).
3. **Gmail reste la messagerie de l'entreprise** : prémisse héritée de
   [D-004] (axe 2). Une migration vers Outlook / Exchange invaliderait
   une partie de ces huit règles et en imposerait d'autres.
4. **Aucun contournement non documenté** : si une règle est enfreinte
   pour une raison légitime, elle est enfreinte **explicitement**, avec
   son motif — pas silencieusement dans un coin du template.
5. **L'email reste inerte** : ni script, ni formulaire, ni pixel, quelle
   que soit l'évolution du support côté client.

## Conséquences

- `template.html` et les snippets sont écrits en tables et en styles
  inline ([D-005]) : ce n'est pas un choix esthétique.
- Les logos sont des PNG en base64 plutôt que des SVG, d'où [D-007].
- Le contrôle de conformité passe **par Gmail**, pas seulement par un
  navigateur : le navigateur valide la structure, Gmail valide ce que le
  destinataire verra.

## Sources

Internes : [D-002] (régime d'antériorité), [D-004] (cible de rendu
unique, dont ces contraintes sont la déclinaison), [D-005] (markup du
gabarit), [D-007] (base64, conséquence du rejet du SVG et consommateur
du budget de poids).
Externes : comportement documenté de Gmail — stripping de `<style>`,
des styles modernes, des `<svg>` inline, de `@font-face`, du JS et des
iframes ; troncature à 102 Ko ; support des data URI.

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici. Les valeurs citées (600px, 102 Ko, liste des éléments
strippés) sont **le texte de la règle**, non des mesures faites dans
cette session.
