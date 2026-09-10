# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this
repository.

## Nature du dépôt

Ce dépôt n'est **pas une application** : c'est une **skill Claude** (`skill/`) qui produit une
communication interne Epiconcept sous forme d'email HTML autonome, prêt à coller dans Gmail. Le
livrable réutilisable est le dossier `skill/` ; `documentation/` est la référence de maintenance
(non embarquée dans la skill, qui doit rester autonome).

Aucun gestionnaire de paquets, aucun build, aucune suite de tests, aucun linter n'est configuré :
le dépôt contient du Markdown, deux fichiers HTML et trois PNG. Le CI
(`.github/workflows/release.yml`) se déclenche uniquement sur publication d'une release GitHub et
zippe `skill/` pour l'upload dans Claude AI.

Il n'existe **aucun test automatique**. Le seul contrôle est le rendu : ouvrir le `.html` produit
dans un navigateur, puis le coller dans Gmail — cf. la checklist de
`documentation/PREREQUIS_TECHNIQUES.md`.

## Architecture

### Trois fichiers, trois rôles disjoints

- `skill/SKILL.md` — le **workflow et les règles** : quand se déclencher, quoi demander, comment
  assembler, quelles couleurs, quelles contraintes Gmail.
- `skill/assets/template.html` — le **gabarit** : la structure verticale en sept blocs, le
  conteneur 600px, les styles inline, les placeholders `{{…}}`.
- `skill/references/editorial-patterns.md` — le **guide éditorial** : ton, structure type,
  emoji-ancres, signature, lignes de sujet, formulations proscrites.

La frontière est stricte ([D-005](documentation/adr/D-005-gabarit-a-placeholders-et-snippets.md),
[D-010](documentation/adr/D-010-conventions-editoriales-externalisees.md)) : **la forme dans le
gabarit, la procédure dans les règles, la voix dans les références.** Une valeur de padding
recopiée dans `SKILL.md` divergera du template ; une consigne de ton glissée dans
`SKILL.md` doublera `editorial-patterns.md`.

### La règle la plus importante : les blobs base64 ne passent pas par le contexte

`skill/assets/banner-fallback-snippets.html` embarque le logo « e » en data URI, deux fois
(~6 000 caractères par blob). `SKILL.md:54` interdit de les réécrire, et impose l'assemblage par
le shell ([D-007](documentation/adr/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md)).

Concrètement, à l'exécution : Claude écrit **seulement** les fragments éditoriaux
(`content_fr.html`, `content_en.html`, `signature.html`), puis un script `awk` / `sed` /
`python3` extrait les snippets, substitue les placeholders et écrit le fichier final. Jamais
d'appel d'écriture avec le HTML complet.

Cette interdiction vise **l'exécution de la skill, pas la maintenance du dépôt** : en tant que
Claude Code travaillant ici, ouvrir les snippets est légitime — mais filtrer les blobs avant de
les afficher (`sed 's/base64,[A-Za-z0-9+/=]\{30,\}/base64,<BLOB>/g'`) évite de gaspiller le
contexte pour rien.

> **Toute modification d'un placeholder ou d'un délimiteur de snippet doit être répercutée dans
> `SKILL.md` et dans l'en-tête de commentaires du template.** La documentation *est* l'interface
> d'assemblage — un `{{PLACEHOLDER}}` renommé sans mise à jour casse l'email silencieusement : le
> texte de substitution reste visible dans le livrable.

### Deux points de contrôle utilisateur, pas un

Le workflow impose **deux** arrêts ([D-006](documentation/adr/D-006-interrogation-prealable-et-validation-du-brouillon.md)) :

1. les six questions de cadrage **avant** de rédiger (`SKILL.md:26-37`) ;
2. le **brouillon texte** soumis à validation **avant** de générer le HTML (`SKILL.md:50`).

Ne pas fusionner les deux, ne pas sauter le second : « la mise en page coûte cher à refaire, le
texte est plus rapide à itérer ». Une correction de fond après assemblage impose de tout
réassembler.

### Le turquoise est un invariant de marque, pas un choix de style

`#4BCDDB` en **fond des deux bandeaux uniquement** ; corps du mail blanc ; `#246589` réservé au
**texte** des titres ; footer 10px `#bbbbbb`
([D-009](documentation/adr/D-009-turquoise-reserve-aux-bandeaux.md)). Pas de dégradé
turquoise → bleu foncé, pas de bloc intermédiaire coloré, jamais de bleu foncé en fond.

Ces valeurs viennent de la charte portée par `ct-epi-visual` : c'est **elle** qui fait autorité,
ce dépôt en est consommateur (cf. `documentation/MAINTENANCE_GABARIT.md`).

## Invariants à ne pas casser

- **Déclenchement opt-in strict ([D-003](documentation/adr/D-003-declenchement-opt-in-strict.md))** :
  la skill ne s'active jamais d'elle-même, même sur un email interne Epiconcept manifeste. La
  `description` de la frontmatter est un contrat, pas une formulation à « rendre plus naturelle » —
  la reformuler réactive le déclenchement automatique. Toute modification passe par un ADR
  d'amendement.
- **Jamais de base64 en contexte ([D-007](documentation/adr/D-007-assemblage-par-le-shell-jamais-de-base64-en-contexte.md))** :
  assemblage en shell, fragments éditoriaux seuls écrits par Claude.
- **HTML autonome, cible Gmail ([D-004](documentation/adr/D-004-html-autonome-cible-gmail.md))** :
  un fichier, zéro dépendance externe, zéro ESP. Tables, styles inline, 600px, < 102 Ko.
- **Turquoise réservé aux bandeaux ([D-009](documentation/adr/D-009-turquoise-reserve-aux-bandeaux.md))**,
  et logo « e » = image officielle — jamais reproduit par formes dessinées, jamais en SVG inline
  (Gmail le strippe).
- **Deux points de contrôle utilisateur ([D-006](documentation/adr/D-006-interrogation-prealable-et-validation-du-brouillon.md))** :
  cadrage puis brouillon texte. Ne pas inventer le contenu manquant, le demander.
- **`skill/` autonome ([D-001](documentation/adr/D-001-mise-sous-depot-de-la-skill.md))** : la
  skill ne référence jamais `documentation/`. Un zip de `skill/` seul doit fonctionner.
- **Environnement cible = Claude AI ([D-012](documentation/adr/D-012-environnement-cible-claude-ai.md))** :
  les chemins `/mnt/skills/user/…`, `/home/claude/`, `/mnt/user-data/outputs/` et les outils
  `bash_tool` / `ask_user_input_v0` / `present_files` sont assumés, pas à « rendre portables ».
- **Aucun secret** dans la skill, et **aucun tracking** dans les emails produits : pas de pixel,
  pas de JS, pas de formulaire.
- **Langue** : documentation, commentaires et libellés en **français accentué**. Ne jamais omettre
  les accents (« résumé exécutif », pas « resume executif »). Exceptions : code, noms de
  fichiers/classes, termes techniques anglais sans équivalent, et les libellés anglais volontaires
  du gabarit (« View this email in your browser », « SMART HEALTH »).

## Décisions structurantes (ADR)

Les décisions de ce dépôt sont tracées dans `documentation/adr/`, selon la convention `D-NNN` du
kit `ct-ai-adr-management` (adoptée par [D-002](documentation/adr/D-002-convention-adr-et-tracage-retroactif.md)) :

- **Index** : `documentation/adr/README.md` — une ligne par ADR, plus la table des patterns
  inscrits.
- **Méthodologie** : `documentation/adr/adr-guide.md` — quand ouvrir un ADR, les 3 types, les
  workflows de création et d'amendement, les anti-patterns. **À lire avant d'en ouvrir un.** C'est
  une copie verbatim du kit : ne pas la retoucher (ses mentions de `scripts/`, de la CI
  `adr-check` et du schema JSON décrivent le kit, pas ce dépôt, qui les a écartés).
- **Templates** : `_template_cadrage.md`, `_template_closure.md`, `_template_amendement.md`.

Règles de travail à respecter ici :

- **Un ADR = un commit**, index mis à jour au même commit. Message : `docs(decisions): <slug>
  (D-NNN)`.
- **Jamais renuméroter** un ADR publié ; jamais réécrire le corps d'un ADR publié. Un amendement
  est un nouvel ADR (`type: amendement`, `amends: D-YYY`) qui met à jour `amended_by` et `status`
  de sa cible **au même commit**, sans toucher son corps.
- **Vérifier les prémisses dans le contenu de la skill**, pas dans un ADR antérieur : citer
  `fichier:ligne`.
- **Ne jamais fabriquer une citation déclencheuse ni des minutes de décision.** Les minutes sont
  les arbitrages de l'opérateur, pas les tiens. Un ADR sans déclencheur conversationnel cite
  l'artefact — c'est le régime d'antériorité posé par D-002 pour D-003 à D-012, dont les minutes
  d'origine sont déclarées indisponibles.
- **Identifiants locaux au dépôt** : un `D-NNN` cité sans nom de dépôt désigne celui d'ici. Les
  dépôts voisins (`ct-epi-visual`, `ct-fwk-specs-fonct`) ont leur propre série ; toute référence
  croisée se qualifie (« `ct-epi-visual` D-005 »).
- Le `CHANGELOG.md` versionne le **gabarit**, pas les décisions : ne pas y lister d'ADR.

## Duplications et maintenance

`documentation/MAINTENANCE_GABARIT.md` porte la **carte des sources de vérité** : quels fichiers
toucher pour chaque type d'évolution, quelles valeurs sont dupliquées volontairement (les couleurs
et les dimensions figurent à la fois dans `SKILL.md` et dans les fichiers HTML), la **dépendance
non déclarée à la charte `ct-epi-visual`**, et les **divergences connues** de la version courante
(inventaire des logos contradictoire, taille du footer différente entre `SKILL.md` et
`editorial-patterns.md`, deux assets sur trois inutilisés).

Toute évolution se répercute dans `CHANGELOG.md` (SemVer appliqué au gabarit : une couleur de
bandeau ou un placeholder qui change est **MAJEUR**) et dans le numéro de version du `README.md`.
