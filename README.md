# ct-epi-com-interne

Gabarit de **communication interne Epiconcept** au format email, livré sous forme de **skill
Claude** : un fichier HTML autonome prêt à coller dans Gmail — bandeau turquoise haut, contenu
mono ou bilingue FR/EN, bandeau turquoise bas, footer légal — plus les conventions éditoriales
maison (ton, emoji-ancres, lignes de sujet, formulations proscrites).

**Version : 1.0.0** · Statut : en production.

## Principe

La skill est un **gabarit à placeholders + des snippets de bandeaux + un guide éditorial**. À
l'invocation, Claude interroge l'utilisateur, fait valider un brouillon **texte**, puis assemble le
HTML final en substituant les fragments dans le template.

| Étape | Ce que fait la skill |
|-------|----------------------|
| 1. Cadrage | 6 questions : sujet, émetteur, email de contact, bilingue ou non, bandeau haut, bandeau bas |
| 2. Rédaction | brouillon **texte** soumis à validation — la mise en page coûte cher à refaire |
| 3. Assemblage | fragments éditoriaux + `assets/template.html` + snippets de bandeaux, assemblés **par le shell** |
| 4. Livraison | un `.html` unique, self-contained, à coller dans Gmail ou à ouvrir dans un navigateur |

Trois principes structurants :

- **Déclenchement explicite uniquement.** La skill ne s'active jamais d'elle-même, même sur un
  email interne Epiconcept manifeste. Soit l'utilisateur la nomme (« /epi-com-interne »), soit
  Claude **demande l'autorisation avant** de produire le livrable. Il est interdit de produire un
  brouillon nu puis de proposer de le retransformer en email brandé.
- **Les blobs base64 ne passent jamais par le contexte.** Les logos sont embarqués en data URI
  dans les snippets (~6 000 caractères chacun). Claude ne génère que les fragments éditoriaux ;
  l'assemblage se fait en shell, `cat` / `sed` / `python3`, jamais par réécriture du HTML complet.
- **Le turquoise appartient aux bandeaux.** `#4BCDDB` en fond des deux bandeaux uniquement, corps
  du mail blanc, `#246589` réservé au **texte** des titres. Pas de dégradé, pas de bloc
  intermédiaire coloré.

## Contenu du dépôt

```
ct-epi-com-interne/
├── README.md                      ← ce fichier
├── CHANGELOG.md                   ← historique des versions du gabarit
├── CLAUDE.md                      ← consignes de travail pour Claude Code sur ce dépôt
├── docs/                          ← documentation de référence (hors skill)
│   ├── PREREQUIS_TECHNIQUES.md      ← environnement, assemblage local, contrôles de rendu
│   ├── MAINTENANCE_GABARIT.md       ← où modifier quoi quand le gabarit évolue
│   └── decisions/                   ← décisions structurantes, convention D-NNN
│       ├── README.md                  ← index des ADR + patterns inscrits
│       ├── adr-guide.md               ← méthodologie (quand/comment ouvrir un ADR)
│       ├── _template_*.md             ← cadrage / closure / amendement
│       └── D-001…D-013.md             ← les décisions
├── tests/
│   ├── tests_unitaires/             ← tests pytest des scripts (vide : la skill n'a pas de code)
│   └── empirical_tests/             ← tests à jouer à la main : non-régression du comportement
└── skill/                         ← la skill Claude (seul dossier embarqué dans le zip de release)
    ├── SKILL.md                     ← workflow, règles de couleur, contraintes Gmail
    ├── assets/
    │   ├── template.html            ← gabarit HTML à placeholders {{…}}
    │   ├── banner-fallback-snippets.html  ← bandeaux haut/bas/variantes/divider (logos en base64)
    │   ├── logo-e-white.png         ← logo « e » blanc — le seul utilisé au rendu
    │   ├── logo-e-turquoise.png     ← variante turquoise (réserve)
    │   └── logo-e-original.png      ← logo original (référence)
    └── references/
        └── editorial-patterns.md    ← ton, structure, emoji-ancres, sujets, interdits
```

> La skill est **autonome** : elle ne dépend ni de `docs/` ni de `tests/`. Ces éléments sont
> fournis dans le dépôt à titre de référence et de maintenance.

## Prérequis

- **Claude AI**, avec la skill installée. C'est l'**environnement cible** : l'assemblage passe par
  un shell (`bash_tool`) avec `awk` / `sed` / `python3`, lit les assets depuis
  `/mnt/skills/user/epi-com-interne/`, travaille dans `/home/claude/` et livre dans
  `/mnt/user-data/outputs/`. Hors de Claude AI, la skill fonctionne en mode dégradé —
  transposition dans `docs/PREREQUIS_TECHNIQUES.md`.
- **Aucun ESP, aucune dépendance externe** : ni Mailchimp, ni CDN, ni police web. Le `.html`
  produit est self-contained (logos en data URI).
- Pour la relecture : un navigateur, et **Gmail** pour le contrôle réel — c'est la cible.

## Utilisation

1. Installer la skill dans Claude AI (uploader le dossier `skill/`, ou le `.zip` attaché à une
   release GitHub).
2. Dans la conversation : **invoquer explicitement la skill** (« /epi-com-interne ») avec la
   matière disponible, ou répondre « oui » quand Claude propose de formater en com interne.
3. Répondre aux questions de cadrage, **valider le brouillon texte**, récupérer le `.html`.
4. Ouvrir le fichier dans un navigateur pour vérifier le rendu, puis le coller dans Gmail.

Comportement détaillé : `skill/SKILL.md`.

## Périmètre

La skill produit une **communication interne** destinée aux salarié·es d'Epiconcept : annonce,
invitation, rappel, événement, formation, initiative d'un groupe de travail (SAM, Epifun, DSI,
Direction). Elle n'est pas destinée aux :

- **communications externes** — clients, partenaires, presse : ni le ton ni le footer
  (« en tant que salarié·e d'Epiconcept ») ne conviennent ;
- **posts sur les réseaux sociaux** — voir `ong-linkedin-post` pour LinkedIn ;
- **documents bureautiques** — voir `epi-visual` pour DOCX / PPTX / XLSX ;
- **campagnes marketing outillées** — pas de tracking, pas de désinscription, pas d'ESP.

## Sécurité et conformité

- **Aucun secret** dans la skill : ni jeton, ni identifiant, ni URL interne authentifiée.
- Le `.html` produit ne contient **ni JavaScript, ni formulaire, ni iframe, ni pixel de suivi** —
  contrainte Gmail autant que choix de sobriété.
- Les logos sont des **actifs de marque Epiconcept** : usage interne, pas de redistribution hors
  de l'entreprise. La palette et le logo « e » officiel font autorité dans `ct-epi-visual` — voir
  `docs/MAINTENANCE_GABARIT.md`.
- Une com interne peut viser une population entière : relire la liste de diffusion et le contenu
  avant envoi. La skill produit le fichier, elle n'envoie rien.
