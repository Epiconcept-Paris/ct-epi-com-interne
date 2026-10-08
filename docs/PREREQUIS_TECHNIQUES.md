# Prérequis techniques et contrôles

Ce document décrit ce dont la skill `epi-com-interne` a besoin pour produire un email conforme, et
comment vérifier le résultat. La skill ne produit qu'un fichier **HTML autonome** : aucune
dépendance externe, aucun ESP, aucune police web.

## Dépendances

| Besoin | Dépendance | Obligatoire ? | Sans elle |
|--------|-----------|---------------|-----------|
| Assemblage du HTML | un shell avec `sed`, `python3` | **oui** | il faudrait réécrire le HTML complet, donc les blobs base64 — interdit ([D-007](decisions/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md)) |
| Questions de cadrage | outil `ask_user_input_v0` | non | poser les questions en conversation, en un seul message |
| Livraison du fichier | outil `present_files` | non | livrer par le mécanisme de l'hôte |
| Contrôle de rendu | un navigateur **et** Gmail | **oui** en pratique | aucun contrôle : il n'existe pas de validateur |
| Palette et logo officiels | skill / dépôt `ct-epi-visual` | non à l'exécution | les valeurs sont déjà recopiées ici ; `ct-epi-visual` reste la source de vérité |

Aucune installation : ni `npm`, ni `pip`, ni compilation. Les logos sont déjà embarqués en data URI
dans les snippets.

## Environnement d'exécution

L'assemblage suppose un système de fichiers et un shell. Les chemins de `skill/SKILL.md` visent
**Claude AI** ([D-012](decisions/D-012-environnement-cible-claude-ai.md)) :

| Ligne | Chemin / outil | Rôle |
|-------|----------------|------|
| `SKILL.md:26` | outil `ask_user_input_v0` | les cinq questions de cadrage |
| `SKILL.md:59-62` | `/home/claude/content_fr.html`, `content_en.html`, `signature.html` | fragments éditoriaux écrits par Claude |
| `SKILL.md:75` | `/mnt/skills/user/epi-com-interne/assets` | lecture du template et des snippets |
| `SKILL.md:69`, `:107` | `/mnt/user-data/outputs/com-interne-<slug>.html` | livrable final |
| `SKILL.md:123` | outil `present_files` | présentation du livrable |

C'est **volontaire** : Claude AI est l'environnement cible, et ces chemins y sont fiables.

Ils **n'existent pas** ailleurs (Claude Code local, Claude Desktop sur dépôt monté). Une exécution
hors Claude AI reste possible, en mode dégradé, à condition de transposer :

- écrire les fragments et le livrable dans le répertoire de travail plutôt que dans
  `/home/claude/` et `/mnt/user-data/outputs/` ;
- lire le template et les snippets depuis l'emplacement réel d'installation de la skill ;
- poser les cinq questions en conversation plutôt que via `ask_user_input_v0`, et livrer le
  fichier par le mécanisme de l'hôte au lieu de `present_files`.

La règle qui ne se transpose pas, elle, reste entière : **ne jamais réécrire les blobs base64**.
Elle ne dépend d'aucun chemin.

## Produire un email

1. **Invoquer explicitement** la skill (« /epi-com-interne ») — elle ne se déclenche jamais seule
   (cf. [D-003](decisions/D-003-declenchement-opt-in-strict.md)).
2. **Répondre aux cinq questions** de cadrage : sujet/titre court, émetteur, email de contact,
   bilingue FR/EN ou non, bandeau haut (image fournie ou fallback). Ce qui est déjà
   dans le prompt initial n'est pas redemandé (`SKILL.md:36`).
3. **Valider le brouillon texte** — second point de contrôle, avant toute mise en page
   ([D-006](decisions/D-006-interrogation-prealable-et-validation-du-brouillon.md)).
4. Laisser l'assemblage se faire en shell, puis récupérer le `.html`.

Squelette d'assemblage, pour mémoire — la version de référence est dans `SKILL.md:73-109` :

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

- [ ] le bandeau haut s'affiche, logo « e » blanc visible et centré ;
- [ ] largeur du conteneur à 600px, pas de débordement horizontal ;
- [ ] rendu correct sur Gmail web **et** mobile (iOS ou Android) ;
- [ ] aucune mention `{{…}}` résiduelle dans le livrable — un placeholder non substitué reste
      visible en clair ;
- [ ] aucun repère de snippet (`<!-- BEGIN:… -->` / `<!-- END:… -->`) resté dans le corps ;
- [ ] en bilingue, la version EN est **dans** la carte blanche, sous le divider — pas au-dessus
      du bandeau ([D-014](decisions/D-014-amendement-d005-structure-sans-bandeau-bas.md)).

**Structure** ([D-005](decisions/D-005-gabarit-a-placeholders-et-snippets.md))

- [ ] les cinq blocs sont présents et dans l'ordre : bandeau haut → titre (+ mention
      bilingue) → contenu FR → *(divider + contenu EN)* → signature → footer légal ;
- [ ] si mono-langue : `{{LANG_NOTICE}}`, `{{LANG_DIVIDER}}` et `{{CONTENT_EN}}` sont **vides**,
      pas laissés en placeholder ni remplis d'un texte de remplissage ;
- [ ] le footer porte les deux lignes légales (copyright, mention salarié·e) — ni adresse postale ni email de contact, qui figure dans la signature.

**Couleurs** ([D-009](decisions/D-009-turquoise-reserve-aux-bandeaux.md))

- [ ] `#4BCDDB` **uniquement** en fond du bandeau haut — jamais dans le corps, jamais en bas, jamais en
      dégradé ;
- [ ] corps du mail sur fond blanc `#ffffff` ;
- [ ] `#246589` uniquement en **texte** (H1), jamais en fond ;
- [ ] footer dans la carte blanche, juste sous la signature, sous un trait gris fin : 11px, `#999999`.

**Éditorial** ([D-010](decisions/D-010-conventions-editoriales-externalisees.md))

- [ ] salutation « Bonjour à toutes et à tous, » ;
- [ ] 3 à 5 emoji-ancres maximum dans le corps, un par ancre ;
- [ ] dates et chiffres clés en `<strong>` ;
- [ ] un call-to-action explicite, jamais « Cliquez ici » ;
- [ ] aucune formulation proscrite (« Cher·e·s collègues », « Bien cordialement », « Veuillez
      trouver ci-joint ») ;
- [ ] ligne de sujet au format `[emoji] [PRÉFIXE] titre orienté action`.

**Bilingue**, le cas échéant ([D-011](decisions/D-011-bilingue-fr-en-par-adaptation.md))

- [ ] FR en premier, mention `🇬🇧 english version below` sous le titre ;
- [ ] divider entre les deux versions ;
- [ ] l'EN a le **même nombre de sections** que le FR, et lit comme une adaptation, pas comme une
      traduction littérale.

**Poids** ([D-004](decisions/D-004-html-autonome-cible-gmail.md))

- [ ] fichier < 102 Ko — au-delà, Gmail affiche « [Message clipped] » et tronque l'email :

```bash
wc -c com-interne-*.html
```

Le logo du bandeau haut en base64 pèse ~6 Ko à lui seul ; le budget restant est confortable pour du
texte, mais s'épuise vite si on ajoute des images embarquées.

## Points de fragilité

- **Extraction des snippets par repères** — `SKILL.md` (étape 3) extrait chaque snippet entre
  ses repères `<!-- BEGIN:NOM -->` / `<!-- END:NOM -->`. Un repère renommé, supprimé ou dupliqué
  donne un snippet vide ou faux. Après toute modification du fichier de snippets, regénérer un
  email de test et vérifier le bandeau haut et le divider à l'œil.
- **Placeholder non substitué** — `sed` ne signale rien ; le squelette Python de `SKILL.md`
  échoue s'il reste un `{{…}}`, mais seulement s'il est repris tel quel. Le `{{PLACEHOLDER}}`
  oublié se retrouve **visible dans l'email envoyé** : c'est la vérification la plus rentable de
  la checklist.
- **Contenu EN hors cellule** — `content_en.html` doit contenir des paragraphes, jamais de
  `<tr>` : le template le place déjà dans sa cellule. Avant
  [D-014](decisions/D-014-amendement-d005-structure-sans-bandeau-bas.md), il était inséré
  directement entre deux lignes de tableau, et le navigateur l'affichait au-dessus de l'email.
- **Régénération accidentelle des blobs** — écrire le HTML final par un appel d'écriture au lieu du
  shell coûte des milliers de tokens et donne l'impression d'un blocage
  ([D-007](decisions/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md)). Le symptôme :
  une génération qui s'éternise sur du texte incompréhensible.
- **Seuil Gmail de 102 Ko** — troncature silencieuse du point de vue de l'expéditeur : l'email
  part, mais les destinataires voient « [Message clipped] » et perdent la fin, footer inclus.
- **Police Source Sans Pro** — elle n'est pas web-safe en email : Gmail retombe sur Arial. C'est
  le rendu **attendu**, pas un défaut ; ne pas tenter de l'embarquer (Gmail strippe `@font-face`).
- **Images fournies par l'utilisateur** — les variantes `{{IMG_URL}}` pointent une URL externe :
  l'email cesse d'être self-contained, et l'image peut être bloquée par le client
  (« Afficher les images »). Vérifier 600px de large minimum et un `alt` descriptif
  (`SKILL.md:164`).
- **Dépendance non déclarée à `ct-epi-visual`** — la palette et le logo « e » viennent de la
  charte, mais rien dans la skill ne le dit : une évolution de charte ne se propagera pas
  automatiquement. Cf. [MAINTENANCE_GABARIT.md](MAINTENANCE_GABARIT.md).
