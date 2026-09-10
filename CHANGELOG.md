# Changelog

Toutes les évolutions notables de la skill `epi-com-interne` sont consignées dans ce fichier.

Le format suit [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/) et le versionnement
suit [SemVer](https://semver.org/lang/fr/), appliqué au **gabarit email** :

- **MAJEUR** — rupture visuelle ou de contrat : couleur des bandeaux modifiée, bloc de la
  structure verticale ajouté / retiré / déplacé, placeholder `{{…}}` renommé ou supprimé,
  méthode d'assemblage changée, logo remplacé, condition de déclenchement élargie. Les emails
  produits avec la version précédente ne sont plus alignés.
- **MINEUR** — ajout rétrocompatible : nouveau snippet de bandeau, nouvelle variante bilingue,
  nouvel emoji-ancre documenté, nouveau placeholder optionnel.
- **CORRECTIF** — correction sans effet sur le contrat : valeur de padding, formulation d'une
  règle éditoriale, coquille, précision de compatibilité Gmail, exemple ajouté.

## [Non publié]

_Rien pour le moment._

## [1.0.0] — 2026-09-09

Première version **versionnée** de la skill. La skill existait et était utilisée avant cette
date, mais hors gestion de version : son historique antérieur n'est pas reconstitué ici.
`1.0.0` décrit donc l'**état du gabarit au moment de la mise sous dépôt**, à contenu inchangé.

### Ajouté

- Mise du projet sous dépôt Git : `README.md`, `CLAUDE.md`, ce `CHANGELOG.md`,
  `.gitignore`, `documentation/` (prérequis techniques, guide de maintenance, ADR) et le
  workflow GitHub `release.yml` qui empaquette `skill/` en `.zip` à chaque release publiée.
- Adoption de la convention ADR `D-NNN` du kit `ct-ai-adr-management`, en version minimale
  (guide, templates, index — sans scripts ni CI), et traçage des décisions structurantes
  existantes en `D-001` à `D-012` (`documentation/adr/`).

### Modifié

- Le dossier de la skill est désormais `skill/` (précédemment `epi-com-interne/`), pour aligner
  le dépôt sur la convention des autres dépôts de skills Epiconcept (`ct-epi-visual`,
  `ct-fwk-specs-fonct`, `ct-ssi-tableau-de-bord`) et permettre au workflow de release de cibler
  un chemin stable. **Le contenu de la skill n'a pas été touché.**

### Contenu du gabarit à cette version

- **Workflow** (`skill/SKILL.md`) : déclenchement opt-in strict, six questions de cadrage,
  validation du brouillon texte avant HTML, assemblage en shell, livraison du `.html`.
- **Structure verticale imposée** en sept blocs : preheader « View this email in your
  browser » → bandeau haut → titre + mention bilingue → contenu FR → divider + contenu EN
  (optionnel) → signature → bandeau bas → footer légal.
- **Gabarit** (`assets/template.html`) : conteneur 600px en tables, styles inline, onze
  placeholders (`{{TITLE}}`, `{{VIEW_URL}}`, `{{HEADER_BANNER}}`, `{{LANG_NOTICE}}`,
  `{{CONTENT_FR}}`, `{{LANG_DIVIDER}}`, `{{CONTENT_EN}}`, `{{SIGNATURE}}`,
  `{{FOOTER_BANNER}}`, `{{CONTACT_EMAIL}}`, `{{YEAR}}`) ; les snippets en ajoutent trois
  (`{{TITLE_FOR_BANNER}}`, `{{IMG_URL}}`, `{{ALT_TEXT}}`).
- **Snippets** (`assets/banner-fallback-snippets.html`) : bandeau haut turquoise (logo 56×56 +
  titre + sur-titre « COMMUNICATION INTERNE »), bandeau bas turquoise (logo 44×44 +
  « Epiconcept » + « SMART HEALTH »), variantes à image fournie (`{{IMG_URL}}`,
  `{{ALT_TEXT}}`), divider FR/EN. Logos embarqués en data URI base64.
- **Règle de couleur** : `#4BCDDB` en fond des deux bandeaux uniquement, corps blanc,
  `#246589` réservé au texte des titres, footer 10px `#bbbbbb`.
- **Contraintes Gmail** : tables pour le layout, styles inline, 600px, poids < 102 Ko,
  dimensions d'images en attributs HTML, pas de SVG inline, pas de JS / formulaire / iframe.
- **Conventions éditoriales** (`references/editorial-patterns.md`) : ton chaleureux et
  collectif, structure type, table de seize emoji-ancres, formats de signature, patterns de
  ligne de sujet (`[emoji] [PRÉFIXE] titre orienté action`), règles du bilingue FR/EN par
  adaptation, footer standard, liste de formulations proscrites.
