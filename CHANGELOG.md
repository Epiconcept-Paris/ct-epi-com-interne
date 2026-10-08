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

Incrément **MAJEUR** (prévu en `2.0.0`) : un bloc de la structure verticale est retiré, deux
placeholders sont supprimés.

### 🎯 Vue synthétique (pour les utilisateurs)

- **Action requise** : réinstaller le `skill.zip` de la release 2.0.0 une fois publiée.
- **Plus de bandeau turquoise en bas de l'email.** Seul le bandeau du haut reste ; Claude ne
  pose donc plus que cinq questions de cadrage.
- **Le footer légal fait désormais partie de la carte, et se réduit à deux lignes** (copyright,
  mention salarié·e) : l'adresse postale et l'email de contact, déjà présent dans la signature,
  sont retirés.
  Il suit directement la signature, sous un trait gris fin, en gris un peu plus lisible : il ne
  paraît plus détaché de l'email une fois collé dans Gmail.
- **Plus de lien « View this email in your browser »** en tête d'email : il ne menait nulle part.
- **En bilingue, la version anglaise s'affiche enfin au bon endroit**, sous le séparateur — elle
  sortait auparavant au-dessus du bandeau.
- **Bandeau haut plus fiable** : la méthode d'extraction documentée produisait un bandeau vide ;
  elle est remplacée par des repères explicites.

### 📝 Détails techniques (pour les mainteneurs)

<details>
<summary>Voir les changements</summary>

#### Modifié (gabarit — MAJEUR)

- `skill/assets/template.html` : lignes preheader (`{{VIEW_URL}}`) et bandeau bas
  (`{{FOOTER_BANNER}}`) supprimées ; adresse postale et lien `{{CONTACT_EMAIL}}` retirés du footer —
  huit placeholders au lieu de onze ; footer légal dans la
  carte, sous la signature, `border-top:1px solid #e5e5e5`, 11px `#999999` ; `bgcolor` en
  attribut sur le conteneur et les cellules ; en-tête de commentaires à jour.
- `skill/assets/banner-fallback-snippets.html` : bandeau bas et variante image bas supprimés ;
  repères `<!-- BEGIN:NOM -->` / `<!-- END:NOM -->` autour de `HEADER`, `HEADER_IMG`,
  `DIVIDER` ; blob du bandeau haut inchangé (vérifié par SHA-1).
- `skill/SKILL.md` : structure en cinq blocs ; question « bandeau du bas » retirée ; tableau des
  couleurs, sections bandeau bas et footer, inventaire des logos mis à jour ; `description` :
  « bandeau bas » → « signature », dispositif d'opt-in inchangé.
- `skill/references/editorial-patterns.md` : footer 11px `#999999`, position sous la signature.

#### Corrigé

- `{{CONTENT_EN}}` était inséré entre deux `<tr>` sans cellule : le navigateur l'affichait hors
  du conteneur, au-dessus de l'email. Il a maintenant sa propre `<td>`.
- L'exemple d'extraction `awk` de `SKILL.md` refermait sa plage sur l'en-tête du snippet et
  produisait un bandeau vide. Remplacé par `sed -n` sur les repères `BEGIN:` / `END:`, avec arrêt
  si le bandeau sort vide.
- Le squelette Python substituait aussi les placeholders cités dans l'en-tête de commentaires du
  template (bandeau et blob dupliqués dans le commentaire) : l'en-tête est retiré avant
  substitution, et l'assemblage échoue s'il reste un `{{…}}`.
- Divergences connues n° 1 (inventaire des logos), n° 2 (taille du footer) et n° 5
  (`{{VIEW_URL}}` sans archive) résolues — cf. `docs/MAINTENANCE_GABARIT.md`.

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
- Réorganisation du dépôt ci-dessus : sans effet sur `skill/`.

</details>

### 🔄 Compatibilité ascendante

- Les emails déjà produits gardent leur bandeau bas et leur preheader : ils ne sont plus alignés
  sur le gabarit, mais rien n'est à reprendre.
- Un assemblage qui fournirait encore `{{VIEW_URL}}`, `{{FOOTER_BANNER}}` ou `{{CONTACT_EMAIL}}` n'a plus d'effet ;
  `content_en.html` ne doit plus contenir de `<tr>`.

### 🔬 Validé empiriquement

- Emails de test mono-langue et bilingue assemblés selon la nouvelle procédure (extraction par
  repères, injection, neutralisation de l'en-tête) et inspectés dans un navigateur : tous les
  blocs dans le conteneur 600px, aucun placeholder résiduel, ni bandeau bas ni preheader.
  **Non vérifié : le rendu après collage dans Gmail**, ni une exécution réelle de la skill dans
  Claude AI.

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
