# Maintenance du gabarit — où modifier quoi

Le gabarit vit dans **quatre fichiers** : les règles (`skill/SKILL.md`), le template
(`skill/assets/template.html`), les snippets de bandeaux
(`skill/assets/banner-fallback-snippets.html`) et le guide éditorial
(`skill/references/editorial-patterns.md`). Plusieurs valeurs y apparaissent **plus d'une fois**
par construction. Ce document dit où intervenir pour chaque type d'évolution, et signale les
duplications à resynchroniser.

## Carte des sources de vérité

| Ce qui change | Fichier(s) à modifier |
|---------------|-----------------------|
| Le turquoise des bandeaux (`#4BCDDB`) | `SKILL.md` (§« Règle de couleurs », §« Bandeau du haut », §« Bandeau du bas ») **et** les deux `background-color` de `banner-fallback-snippets.html` — **et vérifier d'abord la charte `ct-epi-visual`**, qui fait autorité |
| Le bleu foncé du titre (`#246589`) | `SKILL.md` (§« Règle de couleurs ») **et** le `color` du `<h1>` de `template.html` |
| La couleur / taille du footer légal | `template.html` (bloc footer) + `SKILL.md` §« Footer copyright » + `references/editorial-patterns.md` §« Footer standard » — **trois endroits, aujourd'hui divergents** (voir ci-dessous) |
| Le texte des mentions légales (copyright, salarié·e, adresse) | `template.html` (bloc footer) **et** `references/editorial-patterns.md` §« Footer standard » |
| Un bloc de la structure verticale (ajout, retrait, déplacement) | `template.html` (le `<tr>`) **puis** `SKILL.md` §« Ce qu'il faut produire » (la liste numérotée) **puis** l'en-tête de commentaires de `template.html` si un placeholder est concerné ; **MAJEUR** au sens SemVer |
| Un placeholder `{{…}}` | `template.html` (l'occurrence **et** l'en-tête de commentaires qui les documente) + le dictionnaire `subs` de l'exemple `SKILL.md:90-102` ; **MAJEUR** : un placeholder renommé reste visible en clair dans l'email |
| Les dimensions ou paddings d'un bandeau | `banner-fallback-snippets.html` **et** `SKILL.md` §« Bandeau du haut » / §« Bandeau du bas » (les valeurs y sont recopiées « pour référence ») |
| Le logo embarqué | remplacer le PNG dans `skill/assets/` en **conservant le nom**, puis **régénérer les data URI** des deux snippets — le PNG seul ne suffit pas, c'est le base64 qui est rendu ; puis mettre à jour l'inventaire `SKILL.md:200-207` |
| Ajouter / retirer un snippet | `banner-fallback-snippets.html` **et** la plage d'extraction `awk` de `SKILL.md:80-82` (garde `NR>30` codée en dur — voir « Pièges ») |
| Le ton, la structure type, un emoji-ancre, une formulation proscrite | `references/editorial-patterns.md` uniquement — `SKILL.md` §« Rédiger le contenu » n'en donne qu'un résumé, à ne pas enrichir |
| Les patterns de ligne de sujet | `references/editorial-patterns.md` §« Conventions de sujet » **et** `SKILL.md` §« Conventions de sujet » (dupliqué volontairement) |
| Les règles du bilingue FR/EN | `references/editorial-patterns.md` §« Bilingue » + `SKILL.md` (étape 2) + le snippet `DIVIDER FR / EN` |
| Une contrainte Gmail | `SKILL.md` §« Compatibilité Gmail » ; si elle impose un changement de markup, aussi `template.html` et les snippets |
| La méthode d'assemblage | `SKILL.md` §« Étape 3 » — **passer par un ADR d'amendement**, cf. [D-007](adr/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md) |
| Les conditions de déclenchement | `skill/SKILL.md` frontmatter `description` — **passer par un ADR d'amendement**, cf. [D-003](adr/D-003-declenchement-opt-in-strict.md) |
| Les chemins d'environnement et les outils | `SKILL.md` étapes 1, 3 et 4 — **passer par un ADR d'amendement**, cf. [D-012](adr/D-012-environnement-cible-claude-ai.md) |

## Dépendance à la charte `ct-epi-visual` — non déclarée dans la skill

`#4BCDDB` et `#246589` sont **les couleurs de la charte Epiconcept**, définies dans
`ct-epi-visual/skill/SKILL.md` (§Palette). Le logo « e » y est également l'actif officiel
(`LOGO_e_turquoise.png`, avec l'interdiction explicite de le reproduire à la main).

Ce dépôt en est **consommateur**, et rien dans `skill/SKILL.md` ne le dit. Conséquences :

- une évolution de la charte ne se propage **pas** automatiquement ici ; c'est une action de
  maintenance manuelle, à faire dans le même mouvement ;
- `logo-e-white.png` est une **variante dérivée** (silhouette blanche 80×80) qui n'existe pas
  dans `ct-epi-visual` : si le logo officiel change, cette variante doit être **regénérée**, puis
  ré-encodée en base64 dans les deux snippets ;
- en cas de contradiction, la charte prévaut. La corriger ici sans la corriger là-bas crée deux
  vérités.

## Cinq pièges à connaître

### 1. Remplacer un PNG ne suffit pas

Ce qui est rendu dans l'email, c'est le **data URI** des snippets, pas le fichier PNG. Les trois
PNG de `assets/` sont des **sources** : le rendu ne les lit jamais. Remplacer `logo-e-white.png`
sans régénérer les deux blobs base64 ne change rien au livrable — et laisse le dépôt dans un état
trompeur.

### 2. Les plages d'extraction `awk` sont fragiles

`SKILL.md:80-82` extrait les snippets entre commentaires délimiteurs, avec une garde `NR>30`
**codée en dur**. Ajouter, retirer ou déplacer un snippet décale les numéros de ligne et peut
faire extraire le mauvais bloc, sans aucune erreur. Après toute modification de
`banner-fallback-snippets.html`, produire un email de test et regarder les deux bandeaux.

### 3. Un placeholder renommé ne casse rien — il s'affiche

Aucun outillage ne vérifie que tous les `{{…}}` ont été substitués. Un placeholder renommé dans le
template mais pas dans le dictionnaire d'assemblage se retrouve **visible en clair dans l'email
envoyé**. C'est le mode d'échec le plus embarrassant du gabarit, et il est indétectable sans
relecture du fichier produit.

### 4. Le résumé éditorial de `SKILL.md` ne doit pas grossir

`SKILL.md` §« Rédiger le contenu » résume `editorial-patterns.md` en six puces, et renvoie au
fichier de référence. Enrichir ce résumé au lieu du fichier crée deux guides éditoriaux qui
divergeront — et le résumé, lu en premier, gagnera par accident
([D-010](adr/D-010-conventions-editoriales-externalisees.md)).

### 5. Les chemins `/mnt/…` et `/home/claude/` ne sont pas une négligence

Ils visent le système de fichiers de Claude AI, environnement cible assumé du gabarit
([D-012](adr/D-012-environnement-cible-claude-ai.md)). Ne pas les « rendre portables » au passage
d'une autre modification. La transposition hors Claude AI est documentée dans
[PREREQUIS_TECHNIQUES.md](PREREQUIS_TECHNIQUES.md), pas résolue dans la skill.

## Divergences connues à la version 1.0.0

Relevées lors de la mise sous dépôt. **Non corrigées** : le contenu de la skill a été laissé tel
quel. À arbitrer par le mainteneur.

- **Inventaire des logos contradictoire** — `SKILL.md:164` dit que `logo-e-white.png` est utilisé
  dans les bandeaux **haut et bas**, et `SKILL.md:166` que `logo-e-turquoise.png` « n'est plus
  utilisée ». Mais l'inventaire final `SKILL.md:205-206` annonce toujours
  « `logo-e-white.png` — pour bandeau haut » et « `logo-e-turquoise.png` — pour bandeau bas ».
  Le rendu réel suit `:164` (les deux bandeaux ont un fond turquoise, donc un logo blanc).
  L'inventaire est un reste d'une version antérieure.
- **Taille du footer divergente** — `SKILL.md:154` et `template.html` fixent le footer à
  **10px `#bbbbbb`** ; `references/editorial-patterns.md:128` annonce **11-12px `#999999`**. Le
  template fait foi au rendu ; la référence éditoriale décrit un état antérieur.
- **Deux assets sur trois inutilisés** — `logo-e-turquoise.png` est explicitement en réserve
  (`SKILL.md:166`) et `logo-e-original.png` est « référence, peu utilisé » (`SKILL.md:207`, format
  213×120, différent des deux autres en 80×80). Ils sont conservés dans le `.zip` de release : à
  garder si la réserve a un sens, à retirer sinon.
- **Aucun contrôle automatique** — pas de vérification des placeholders substitués, pas de mesure
  de poids, pas de validateur de rendu email. Tout est manuel, via la checklist de
  [PREREQUIS_TECHNIQUES.md](PREREQUIS_TECHNIQUES.md).
- **`{{VIEW_URL}}` sans archive web** — le preheader « View this email in your browser » est
  toujours présent dans le template, mais aucun mécanisme d'archive n'existe : le lien vaut `#`
  par défaut (`template.html`, en-tête de commentaires). Un lien mort visible en tête d'email —
  à assumer ou à retirer.
- **`{{LANG_NOTICE}}` en anglais, dans un template `lang="fr"`** — le libellé
  « 🇬🇧 English version below » est volontairement anglais, comme
  « View this email in your browser » et « SMART HEALTH ». Ce n'est pas une entorse à la règle de
  langue française du dépôt, mais il vaut mieux le savoir avant de « corriger » ces chaînes.

## Procédure d'évolution

1. Modifier les fichiers listés dans la carte ci-dessus — **tous** ceux de la ligne concernée.
2. Si une couleur ou le logo est touché : **vérifier d'abord `ct-epi-visual`**, qui fait autorité.
3. Produire un email de test, l'ouvrir dans un navigateur **et** le coller dans Gmail, puis
   repasser la checklist de [PREREQUIS_TECHNIQUES.md](PREREQUIS_TECHNIQUES.md).
4. Si la décision est structurante, écrire une ADR dans `documentation/adr/` et l'ajouter à
   l'index. Toucher à la `description` de la frontmatter, à la méthode d'assemblage ou aux chemins
   d'environnement impose un ADR d'**amendement** de
   [D-003](adr/D-003-declenchement-opt-in-strict.md),
   [D-007](adr/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md) ou
   [D-012](adr/D-012-environnement-cible-claude-ai.md).
5. Ajouter une entrée dans `CHANGELOG.md`, en choisissant l'incrément selon les règles SemVer qui
   y sont définies (une couleur de bandeau ou un placeholder qui change est **MAJEUR**).
6. Mettre à jour le numéro de version affiché dans `README.md`.
7. Publier une release GitHub sur le tag : le workflow attache automatiquement `skill.zip`.
