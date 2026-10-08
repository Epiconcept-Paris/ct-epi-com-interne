# Changelog

Toutes les modifications notables de la skill `epi-com-interne` sont documentées ici.

Format basé sur [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/),
versionnage selon [Semantic Versioning](https://semver.org/lang/fr/), appliqué au
**gabarit email** :

- **MAJEUR** — rupture visuelle ou de contrat : couleur des bandeaux modifiée, bloc de la
  structure verticale ajouté / retiré / déplacé, placeholder `{{…}}` renommé ou supprimé,
  méthode d'assemblage changée, logo remplacé, condition de déclenchement élargie. Les emails
  produits avec la version précédente ne sont plus alignés.
- **MINEUR** — ajout rétrocompatible : nouveau snippet de bandeau, nouvelle variante bilingue,
  nouvel emoji-ancre documenté, nouveau placeholder optionnel.
- **CORRECTIF** — correction sans effet sur le contrat : valeur de padding, formulation d'une
  règle éditoriale, coquille, précision de compatibilité Gmail, exemple ajouté.

<!-- STRUCTURE D'UNE ENTRÉE DE VERSION — toujours dans cet ordre :

     ## [X.Y.Z] - AAAA-MM-JJ

     ### 🎯 Vue synthétique (pour les utilisateurs)       ← OBLIGATOIRE
         Ce que l'utilisateur de la skill voit changer, en langage d'usage. Pas de chemin de
         fichier ici. Si une action est requise (réinstaller le zip…), une puce
         **Action requise** en tête.

     ### 📝 Détails techniques (pour les mainteneurs)      ← OBLIGATOIRE
         Ce qui a été modifié, où (`fichier:ligne`), et pourquoi. Replié dans un <details>
         dès que la liste dépasse quelques puces. Sous-sections Keep a Changelog, seulement
         celles qui ont du contenu : Ajouté, Modifié, Corrigé, Supprimé, Déprécié, Sécurité.

     ### 🔄 Compatibilité ascendante                       ← si MAJEUR, ou si doute

     ### 🔬 Validé empiriquement                           ← dès qu'un test a été joué
         Les tests de `tests/empirical_tests/` rejoués pour cette version, avec leur
         résultat. Une version sans cette section n'a été validée par personne.

     Séparer les versions par une ligne `---`. Ajouter le lien de comparaison en pied de
     fichier à chaque release. -->

---

## [Non publié]

### 🎯 Vue synthétique (pour les utilisateurs)

- **Rien ne change à l'usage.** Le dépôt est réorganisé selon le gabarit commun des dépôts de
  skills Epiconcept ; la skill elle-même n'est pas modifiée.

### 📝 Détails techniques (pour les mainteneurs)

<details>
<summary>Voir les changements</summary>

#### Ajouté

- `tests/tests_unitaires/` (vide : la skill n'embarque aucun script) et `tests/empirical_tests/`
  (tests de comportement à jouer à la main ; aucun encore écrit), hors de `skill/`.
- `CLAUDE.md` : sections « Dépôts voisins » et « Tests », conformes au gabarit.

#### Modifié

- `documentation/` devient `docs/`, et `documentation/adr/` devient `docs/decisions/` (chemin
  recommandé par le guide ADR), par alignement sur le gabarit `ct-skill-template`. Liens
  internes de `docs/PREREQUIS_TECHNIQUES.md`, `docs/MAINTENANCE_GABARIT.md`, `README.md` et
  `CLAUDE.md` mis à jour.
- `docs/MAINTENANCE_GABARIT.md` : divergences connues numérotées de façon stable ; procédure
  d'évolution complétée (tests empiriques, deux vues du changelog).
- `.gitignore` aligné sur le gabarit, en conservant les exclusions propres au gabarit email
  (fragments temporaires d'assemblage).
- Ce `CHANGELOG.md` adopte la structure d'entrée du gabarit (vue synthétique / détails
  techniques) ; le contenu de l'entrée `1.0.0` est inchangé.
- **Le contenu de `skill/` n'a pas été touché.**

</details>

---

## [1.0.0] - 2026-09-09

### 🎯 Vue synthétique (pour les utilisateurs)

- **Première version versionnée de la skill.** La skill existait et était utilisée avant cette
  date, mais hors gestion de version : son historique antérieur n'est pas reconstitué ici.
  `1.0.0` décrit l'**état du gabarit au moment de la mise sous dépôt**, à contenu inchangé —
  rien ne change à l'usage.
- **Installation par release** : la skill se télécharge désormais sous forme de `skill.zip`
  attaché à chaque release GitHub, à uploader tel quel dans Claude AI.

### 📝 Détails techniques (pour les mainteneurs)

<details>
<summary>Voir les changements de cette version</summary>

#### Ajouté

- Mise du projet sous dépôt Git : `README.md`, `CLAUDE.md`, ce `CHANGELOG.md`,
  `.gitignore`, `documentation/` (prérequis techniques, guide de maintenance, ADR) et le
  workflow GitHub `release.yml` qui empaquette `skill/` en `.zip` à chaque release publiée.
- Adoption de la convention ADR `D-NNN` du kit `ct-ai-adr-management`, en version minimale
  (guide, templates, index — sans scripts ni CI), et traçage des décisions structurantes
  existantes en `D-001` à `D-012` (`documentation/adr/`).

#### Modifié

- Le dossier de la skill est désormais `skill/` (précédemment `epi-com-interne/`), pour aligner
  le dépôt sur la convention des autres dépôts de skills Epiconcept (`ct-epi-visual`,
  `ct-fwk-specs-fonct`, `ct-ssi-tableau-de-bord`) et permettre au workflow de release de cibler
  un chemin stable. **Le contenu de la skill n'a pas été touché.**

#### Contenu du gabarit à cette version

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

</details>

### 🔬 Validé empiriquement

- Aucun test empirique consigné pour cette version.

---

[Non publié]: https://github.com/Epiconcept-Paris/ct-epi-com-interne/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/Epiconcept-Paris/ct-epi-com-interne/releases/tag/v1.0.0
