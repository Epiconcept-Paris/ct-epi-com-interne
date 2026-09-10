# Prérequis techniques et contrôles

Ce document décrit ce dont la skill `epi-com-interne` a besoin pour produire un email conforme, et
comment vérifier le résultat. La skill ne produit qu'un fichier **HTML autonome** : aucune
dépendance externe, aucun ESP, aucune police web.

## Dépendances

| Besoin | Dépendance | Obligatoire ? | Sans elle |
|--------|-----------|---------------|-----------|
| Assemblage du HTML | un shell avec `awk`, `sed`, `python3` | **oui** | il faudrait réécrire le HTML complet, donc les blobs base64 — interdit ([D-007](adr/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md)) |
| Questions de cadrage | outil `ask_user_input_v0` | non | poser les questions en conversation, en un seul message |
| Livraison du fichier | outil `present_files` | non | livrer par le mécanisme de l'hôte |
| Contrôle de rendu | un navigateur **et** Gmail | **oui** en pratique | aucun contrôle : il n'existe pas de validateur |
| Palette et logo officiels | skill / dépôt `ct-epi-visual` | non à l'exécution | les valeurs sont déjà recopiées ici ; `ct-epi-visual` reste la source de vérité |

Aucune installation : ni `npm`, ni `pip`, ni compilation. Les logos sont déjà embarqués en data URI
dans les snippets.

## Environnement d'exécution

L'assemblage suppose un système de fichiers et un shell. Les chemins de `skill/SKILL.md` visent
**Claude AI** ([D-012](adr/D-012-environnement-cible-claude-ai.md)) :

| Ligne | Chemin / outil | Rôle |
|-------|----------------|------|
| `SKILL.md:26` | outil `ask_user_input_v0` | les six questions de cadrage |
| `SKILL.md:60-63` | `/home/claude/content_fr.html`, `content_en.html`, `signature.html` | fragments éditoriaux écrits par Claude |
| `SKILL.md:76` | `/mnt/skills/user/epi-com-interne/assets` | lecture du template et des snippets |
| `SKILL.md:72`, `:104` | `/mnt/user-data/outputs/com-interne-<slug>.html` | livrable final |
| `SKILL.md:120` | outil `present_files` | présentation du livrable |

C'est **volontaire** : Claude AI est l'environnement cible, et ces chemins y sont fiables.

Ils **n'existent pas** ailleurs (Claude Code local, Claude Desktop sur dépôt monté). Une exécution
hors Claude AI reste possible, en mode dégradé, à condition de transposer :

- écrire les fragments et le livrable dans le répertoire de travail plutôt que dans
  `/home/claude/` et `/mnt/user-data/outputs/` ;
- lire le template et les snippets depuis l'emplacement réel d'installation de la skill ;
- poser les six questions en conversation plutôt que via `ask_user_input_v0`, et livrer le
  fichier par le mécanisme de l'hôte au lieu de `present_files`.

La règle qui ne se transpose pas, elle, reste entière : **ne jamais réécrire les blobs base64**.
Elle ne dépend d'aucun chemin.

## Produire un email

1. **Invoquer explicitement** la skill (« /epi-com-interne ») — elle ne se déclenche jamais seule
   (cf. [D-003](adr/D-003-declenchement-opt-in-strict.md)).
2. **Répondre aux six questions** de cadrage : sujet/titre court, émetteur, email de contact,
   bilingue FR/EN ou non, bandeau haut (image fournie ou fallback), bandeau bas. Ce qui est déjà
   dans le prompt initial n'est pas redemandé (`SKILL.md:37`).
3. **Valider le brouillon texte** — second point de contrôle, avant toute mise en page
   ([D-006](adr/D-006-interrogation-prealable-et-validation-du-brouillon.md)).
4. Laisser l'assemblage se faire en shell, puis récupérer le `.html`.

Squelette d'assemblage, pour mémoire — la version de référence est dans `SKILL.md:74-106` :

```bash
SKILL=/mnt/skills/user/epi-com-interne/assets
OUT=/home/claude
# extraire les snippets de bandeaux, substituer {{TITLE_FOR_BANNER}}, puis python3 pour
# remplacer les placeholders du template par le contenu des fragments.
```

Les fragments que Claude écrit lui-même sont **uniquement** `content_fr.html`,
`content_en.html` (vide si mono-langue) et `signature.html`.

## Contrôles de conformité

Aucun contrôle n'est automatisé — il n'existe pas de validateur d'email. Relire le fichier produit
contre cette liste.

**Rendu** — ouvrir le `.html` dans un navigateur, **puis** le coller dans Gmail (la cible réelle) :

- [ ] les deux bandeaux s'affichent, logo « e » blanc visible et centré ;
- [ ] largeur du conteneur à 600px, pas de débordement horizontal ;
- [ ] rendu correct sur Gmail web **et** mobile (iOS ou Android) ;
- [ ] aucune mention `{{…}}` résiduelle dans le livrable — un placeholder non substitué reste
      visible en clair ;
- [ ] aucun commentaire de délimiteur de snippet (`<!-- ==== … -->`) resté dans le corps.

**Structure** ([D-005](adr/D-005-gabarit-a-placeholders-et-snippets.md))

- [ ] les sept blocs sont présents et dans l'ordre : preheader → bandeau haut → titre (+ mention
      bilingue) → contenu FR → *(divider + contenu EN)* → signature → bandeau bas → footer légal ;
- [ ] si mono-langue : `{{LANG_NOTICE}}`, `{{LANG_DIVIDER}}` et `{{CONTENT_EN}}` sont **vides**,
      pas laissés en placeholder ni remplis d'un texte de remplissage ;
- [ ] le footer porte les quatre lignes légales (copyright, mention salarié·e, adresse, contact).

**Couleurs** ([D-009](adr/D-009-turquoise-reserve-aux-bandeaux.md))

- [ ] `#4BCDDB` **uniquement** en fond des deux bandeaux — jamais dans le corps, jamais en
      dégradé ;
- [ ] corps du mail sur fond blanc `#ffffff` ;
- [ ] `#246589` uniquement en **texte** (H1), jamais en fond ;
- [ ] footer discret : 10px, `#bbbbbb`.

**Éditorial** ([D-010](adr/D-010-conventions-editoriales-externalisees.md))

- [ ] salutation « Bonjour à toutes et à tous, » ;
- [ ] 3 à 5 emoji-ancres maximum dans le corps, un par ancre ;
- [ ] dates et chiffres clés en `<strong>` ;
- [ ] un call-to-action explicite, jamais « Cliquez ici » ;
- [ ] aucune formulation proscrite (« Cher·e·s collègues », « Bien cordialement », « Veuillez
      trouver ci-joint ») ;
- [ ] ligne de sujet au format `[emoji] [PRÉFIXE] titre orienté action`.

**Bilingue**, le cas échéant ([D-011](adr/D-011-bilingue-fr-en-par-adaptation.md))

- [ ] FR en premier, mention `🇬🇧 english version below` sous le titre ;
- [ ] divider entre les deux versions ;
- [ ] l'EN a le **même nombre de sections** que le FR, et lit comme une adaptation, pas comme une
      traduction littérale.

**Poids** ([D-004](adr/D-004-html-autonome-cible-gmail.md))

- [ ] fichier < 102 Ko — au-delà, Gmail affiche « [Message clipped] » et tronque l'email :

```bash
wc -c com-interne-*.html
```

Les deux logos en base64 pèsent ~12 Ko à eux seuls ; le budget restant est confortable pour du
texte, mais s'épuise vite si on ajoute des images embarquées.

## Points de fragilité

- **Extraction des snippets par plage `awk`** — `SKILL.md:80-82` découpe
  `banner-fallback-snippets.html` entre commentaires délimiteurs, avec une garde `NR>30` codée en
  dur. Ajouter, retirer ou déplacer un snippet **décale les numéros de ligne** et peut faire
  extraire le mauvais bloc, sans erreur. Après toute modification du fichier de snippets,
  regénérer un email de test et vérifier les deux bandeaux à l'œil.
- **Placeholder non substitué** — le mode d'échec est silencieux côté outillage : `sed` ne
  signale rien, le `{{PLACEHOLDER}}` se retrouve **visible dans l'email envoyé**. C'est la
  vérification la plus rentable de la checklist.
- **Régénération accidentelle des blobs** — écrire le HTML final par un appel d'écriture au lieu du
  shell coûte des milliers de tokens et donne l'impression d'un blocage
  ([D-007](adr/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md)). Le symptôme :
  une génération qui s'éternise sur du texte incompréhensible.
- **Seuil Gmail de 102 Ko** — troncature silencieuse du point de vue de l'expéditeur : l'email
  part, mais les destinataires voient « [Message clipped] » et perdent la fin, footer inclus.
- **Police Source Sans Pro** — elle n'est pas web-safe en email : Gmail retombe sur Arial. C'est
  le rendu **attendu**, pas un défaut ; ne pas tenter de l'embarquer (Gmail strippe `@font-face`).
- **Images fournies par l'utilisateur** — les variantes `{{IMG_URL}}` pointent une URL externe :
  l'email cesse d'être self-contained, et l'image peut être bloquée par le client
  (« Afficher les images »). Vérifier 600px de large minimum et un `alt` descriptif
  (`SKILL.md:168`).
- **Dépendance non déclarée à `ct-epi-visual`** — la palette et le logo « e » viennent de la
  charte, mais rien dans la skill ne le dit : une évolution de charte ne se propagera pas
  automatiquement. Cf. [MAINTENANCE_GABARIT.md](MAINTENANCE_GABARIT.md).
