---
id: D-004
title: "Cadrage — un HTML autonome ciblant Gmail, sans ESP ni dépendance externe"
status: accepted
type: cadrage
date: 2026-09-09
supersedes: []
amended_by: []
sources:
  - D-002
patterns:
  - livrable-self-contained-zero-dependance
  - cible-de-rendu-unique-assumee
---

## Contexte / déclencheur

> **Déclencheur non conversationnel** — artefacts :
> `skill/SKILL.md:8` : « Cette skill produit une communication interne
> Epiconcept au format **HTML autonome**, prête à coller dans **Gmail**
> et à prévisualiser dans un navigateur. Aucune dépendance Mailchimp.
> Cible email primaire : **Gmail (Google Workspace Epiconcept)**. » ;
> `skill/SKILL.md:12` : « Un fichier `.html` unique, self-contained ».

Décision **antérieure au dépôt**, tracée rétroactivement le 2026-09-09
sous le régime d'antériorité de [D-002] (axe 4) : la date ci-dessus est
celle du traçage, la date d'acte d'origine n'est pas connue.

Une communication interne peut se diffuser de bien des façons :
plateforme d'emailing (Mailchimp et consorts), intranet, canal de chat,
document joint. Le gabarit tranche pour **un fichier HTML que
l'expéditeur colle lui-même dans Gmail**, et toutes les autres règles du
gabarit découlent de ce choix.

État courant (pré-décision, vérifié) :

- `SKILL.md:172-184` liste huit contraintes techniques, toutes
  justifiées par Gmail : tables pour le layout, styles **inline**
  uniquement (Gmail strippe `<style>` en `<head>` quand on colle
  l'HTML), 600px de large, poids **< 102 Ko** sous peine de
  « [Message clipped] », police avec repli Arial, couleurs en hex 6
  caractères, `width`/`height` en attributs HTML, pas de JS / `<form>` /
  `<iframe>`.
- `SKILL.md:158` justifie le rejet du SVG inline : « Gmail strippe ou
  ignore les `<svg>` inline » — d'où les logos en PNG base64.
- `SKILL.md:183` justifie les data URI : « OK pour les logos (Gmail les
  rend) ».
- `template.html:25-33` implémente : `<table role="presentation">`
  imbriqués, `max-width:600px`, tout le style en attribut `style=`.
- `SKILL.md:180` assume explicitement que Source Sans Pro ne sera pas
  rendue : « Gmail tombera sur Arial (rendu attendu, propre) ».

**Question centrale** : *« quel est le livrable, et pour quel client de
messagerie est-il écrit ? »*

## Décisions actées

### Axe 1 — un fichier, zéro dépendance

Le livrable est **un `.html` self-contained** : logos en data URI,
aucun CDN, aucune police web, aucune image hébergée, aucun script. Il
s'ouvre dans un navigateur pour la relecture et se colle dans Gmail pour
l'envoi.

Les propriétés recherchées :

- **il survit au copier-coller** — c'est le mode de diffusion réel :
  l'expéditeur colle le rendu dans un nouveau message. Une ressource
  externe ne survivrait pas toujours à cette opération, et une image
  distante peut être bloquée par le client ;
- **il n'expire pas** — pas de lien vers un serveur qui pourrait
  disparaître, pas de compte à maintenir ;
- **il ne trace personne** — pas de pixel, pas de redirecteur. Une com
  interne n'a pas à mesurer les ouvertures de ses collègues.

Écarté : Mailchimp ou un autre ESP — impose un compte, une liste, un
export, et introduit du tracking par défaut ; la mention « Aucune
dépendance Mailchimp » (`SKILL.md:8`) indique que l'option a été
écartée. Écarté : images de bandeaux hébergées sur un serveur interne —
rompt l'autonomie, et échoue derrière un client qui bloque les images
distantes. Écarté : PDF ou pièce jointe — n'est pas lu dans le corps du
message.

Pattern *« livrable-self-contained-zero-dependance »*.

### Axe 2 — une cible de rendu unique et nommée : Gmail

Gmail (Google Workspace Epiconcept), web + iOS + Android. Pas
« les clients email en général », pas Outlook.

C'est ce qui rend les contraintes de `SKILL.md:172-184` **décidables**
plutôt que superstitieuses : chaque règle est là parce que Gmail impose
quelque chose, et cite ce qu'il impose. Un gabarit qui viserait « tous
les clients » cumulerait les contournements d'Outlook (VML, conditional
comments, tableaux à trois niveaux) sans que personne ne puisse dire
lesquels sont encore utiles.

Le corollaire est une **dette assumée** : le rendu hors Gmail n'est pas
garanti. Comme l'entreprise est sur Google Workspace, la cible couvre la
quasi-totalité des destinataires — un salarié qui lirait ses mails
ailleurs verrait un email dégradé, pas cassé (les tables et les styles
inline sont le plus petit dénominateur commun de l'email HTML).

Pattern *« cible-de-rendu-unique-assumee »*.

### Axe 3 — les contraintes Gmail sont documentées **avec leur motif**

Chacune des huit règles porte sa raison. Ce n'est pas de la pédagogie :
sans motif, une règle du type « pas de flexbox » se lit comme un
archaïsme et se contourne à la première hésitation. Avec le motif
(« Gmail strippe certains styles modernes »), elle est non négociable
tant que la cause tient.

Deux d'entre elles gouvernent d'autres décisions :

- **pas de SVG inline** → les logos sont des PNG en base64, ce qui rend
  la règle anti-réécriture de [D-007] nécessaire ;
- **< 102 Ko** → un budget de poids, dont ~12 Ko sont déjà consommés
  par les deux logos. Au-delà du seuil, Gmail tronque le message et
  affiche « [Message clipped] » : l'email part, mais les destinataires
  perdent la fin — footer légal inclus.

### Axe 4 — la police est un repli assumé, pas un défaut

`'Source Sans Pro', 'Segoe UI', Arial, sans-serif` : la police de la
charte est déclarée en premier, mais Gmail servira **Arial** dans
presque tous les cas. C'est écrit noir sur blanc comme le « rendu
attendu » (`SKILL.md:180`).

Ne pas tenter de l'embarquer : Gmail strippe `@font-face`, et un
`@font-face` en base64 ferait exploser le budget des 102 Ko pour un
gain nul.

## Conditions de légitimité

1. **Autonomie effective** : le `.html` produit ne contient aucune URL
   externe, sauf `{{IMG_URL}}` quand l'utilisateur fournit sa propre
   image de bandeau — cas où l'autonomie est perdue **par son choix**,
   et où l'image peut être bloquée par le client (`SKILL.md:168`).
2. **Poids sous le seuil** : mesuré, pas supposé
   (`wc -c`). Le seuil de 102 Ko est une valeur Gmail, hors de notre
   contrôle : à revérifier si Google la change.
3. **Gmail reste la messagerie de l'entreprise** : c'est la prémisse de
   l'axe 2. Une migration vers Outlook / Exchange invaliderait la
   moitié des contraintes techniques et imposerait de rouvrir cet ADR.
4. **Le copier-coller reste le mode d'envoi** : si la diffusion passait
   un jour par un ESP, l'arbitrage change (styles en `<head>`
   redeviennent possibles, le tracking devient un sujet de conformité).
5. **Aucun tracking introduit** : ni pixel, ni lien redirigé. C'est une
   propriété du livrable, à vérifier si un jour on ajoute un lien
   d'archive web.

## Conséquences

- Tout le markup du gabarit est en tables et en styles inline
  ([D-005]) : ce n'est pas un choix esthétique mais la conséquence de
  l'axe 2.
- Les logos sont des PNG base64, donc lourds et non compressibles, donc
  interdits à la réécriture ([D-007]).
- La checklist de contrôle est **manuelle et passe par Gmail** : il
  n'existe pas de validateur d'email
  (`documentation/PREREQUIS_TECHNIQUES.md`).
- Le preheader « View this email in your browser » est un vestige de
  l'univers ESP : sans archive web, `{{VIEW_URL}}` vaut `#` — divergence
  connue, cf. `documentation/MAINTENANCE_GABARIT.md`.

## Sources

Internes : [D-002] (régime d'antériorité), [D-005] (gabarit en tables,
conséquence directe), [D-007] (blobs base64, conséquence du rejet du
SVG), [D-009] (couleurs en hex, contrainte Gmail).
Externes : comportement documenté de Gmail — stripping de `<style>` et
des `<svg>` inline, troncature à 102 Ko, support des data URI.

## Minutes de décision

**Minutes d'origine non disponibles.** Cette décision est antérieure au
dépôt ; ni la conversation ni les arbitrages qui l'ont produite ne sont
conservés. Conformément à [D-002] (axe 4), aucun échange n'est
reconstitué ici. En particulier, la mention « Aucune dépendance
Mailchimp » atteste que l'option ESP a été écartée, **pas** qu'un
Mailchimp ait été utilisé puis abandonné : rien dans les artefacts ne
permet de l'affirmer.
