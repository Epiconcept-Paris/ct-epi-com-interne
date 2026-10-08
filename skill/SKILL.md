---
name: epi-com-interne
description: "Met en forme une communication interne Epiconcept en email HTML (Gmail + prévisualisable navigateur) : bandeau haut, contenu mono/bilingue FR/EN, bandeau bas, footer. ⚠️ NE PAS déclencher automatiquement, même pour un email interne Epiconcept. Utiliser UNIQUEMENT dans l'un de ces deux cas : (a) l'utilisateur la nomme explicitement (« /epi-com-interne », « applique epi-com-interne », « com interne Epiconcept ») ; (b) Claude estime la skill utile : il ARRÊTE sa production, POSE la question en une phrase courte (ex. « Tu veux que je formate ça en com interne Epiconcept (email HTML branded pour Gmail) ? »), ATTEND la réponse, puis applique le choix. ⚠️ La question se pose AVANT de produire le livrable, jamais après. INTERDIT de produire un brouillon texte/markdown sans la skill puis de proposer de le retransformer en email branded avec epi-com-interne. Sans invocation explicite ni réponse positive, produire le contenu sans la mise en forme email branded."
---

# Communication interne Epiconcept — Email

Cette skill produit une communication interne Epiconcept au format **HTML autonome**, prête à coller dans **Gmail** et à prévisualiser dans un navigateur. Aucune dépendance Mailchimp. Cible email primaire : **Gmail (Google Workspace Epiconcept)**.

## Ce qu'il faut produire

Un fichier `.html` unique, self-contained, avec cette structure verticale (de haut en bas) :

1. **Bandeau image haut** — visuel thématique (fourni par l'utilisateur ou fallback HTML branded)
2. **Titre** — en bleu foncé Epiconcept, avec emoji thématique
3. **Corps** — version FR, et optionnellement version EN en dessous (séparée par un divider)
4. **Signature** — équipe/groupe émetteur + email de contact
5. **Footer** — copyright et mention salarié·e Epiconcept (deux lignes, ni adresse postale ni email : le contact est dans la signature) ; dans la même carte blanche que le contenu, sous un trait gris fin

Pas de bandeau en bas, pas de lien « View this email in your browser » : l'email est collé dans Gmail, il n'existe pas de version web à laquelle renvoyer.

## Workflow

### Étape 1 — Interroger avant de rédiger

⚠️ **Avant toute rédaction**, poser ces questions à l'utilisateur via l'outil `ask_user_input_v0` (regrouper en un seul appel quand c'est possible) :

| Question | Pourquoi |
|---|---|
| **Sujet/titre court** (ex. « Calculez votre empreinte carbone ») | Sert au `<title>`, au bandeau haut et au H1 |
| **Émetteur** (ex. « Equipe SAM Environnement (Lore, Fabrice, Maud) » / « Epifun » / « DSI ») | Sert à la signature et au from-name implicite |
| **Email contact** (ex. `sam-environnement@epiconcept.fr`) | Lien cliquable dans la signature |
| **Bilingue FR/EN ?** (oui / non) | Si oui : produire les deux versions (FR puis EN, séparées par un trait + 🇬🇧) |
| **Bandeau du haut** (image fournie / fallback généré) | Si image : demander URL ou chemin local ; si fallback : générer un bandeau HTML/CSS branded |

Si l'utilisateur fournit déjà tous ces éléments dans le prompt initial, ne **pas** redemander — utiliser ce qui est là.

### Étape 2 — Rédiger le contenu

Suivre les conventions éditoriales d'Epiconcept (voir `references/editorial-patterns.md`). En résumé :

- **Salutation** : « Bonjour à toutes et à tous, » (jamais « Salut » ni « Cher·e·s »)
- **Ton** : chaleureux, collectif, factuel — éviter le marketing-speak
- **Structure** : titre principal, paragraphe d'intro/contexte, sections avec emoji-ancre (🎨 / 🏆 / 📋 / 👉 …), call-to-action clair, signature
- **Mise en valeur** : `<strong>` pour les dates et chiffres clés, listes courtes pour les programmes/objectifs
- **Emoji** : utilisés généreusement comme bullets visuels et ancres de section (cohérent avec les exemples observés)
- **Si bilingue** : version EN après FR, séparée par un divider horizontal + marqueur `🇬🇧 english version below`. L'EN est une **adaptation** (pas une traduction littérale) — on garde le ton, on adapte les références culturelles si nécessaire

Présenter d'abord le **brouillon texte** à l'utilisateur pour validation avant de générer le HTML final. C'est un point de contrôle important : la mise en page coûte cher à refaire, le texte est plus rapide à itérer.

### Étape 3 — Rendre le HTML

⚠️ **RÈGLE CRITIQUE — NE JAMAIS retaper les blobs base64.** Les snippets de bandeaux fallback contiennent le logo Epiconcept embarqué en base64 (~6 000 caractères par blob, deux blobs = ~12 000 caractères de bruit non-compressible). Les réécrire via `create_file` coûte des milliers de tokens, ralentit la génération à l'extrême et donne l'impression d'un blocage.

**Méthode imposée : assembler via `bash_tool`** avec `cat` + `sed`. Les fichiers source (template + snippets) sont lus depuis le disque et concaténés/substitués directement — Claude n'a qu'à générer les fragments éditoriaux (contenu FR, contenu EN, signature), pas les blobs binaires.

**Procédure d'assemblage :**

1. Écrire les fragments éditoriaux dans des fichiers temporaires dans `/home/claude/` :
   - `/home/claude/content_fr.html` — corps FR rendu en HTML
   - `/home/claude/content_en.html` — corps EN rendu en HTML (ou fichier vide si mono-langue)
   - `/home/claude/signature.html` — bloc signature

2. Dans `bash_tool`, exécuter un script qui :
   - Extrait de `assets/banner-fallback-snippets.html` le snippet du bandeau haut (`HEADER` en fallback, `HEADER_IMG` si l'utilisateur fournit une image) vers `/home/claude/header.html`, et — si bilingue — le snippet `DIVIDER` vers `/home/claude/divider.html` (fichier vide sinon). Chaque snippet est encadré par deux repères `<!-- BEGIN:NOM -->` / `<!-- END:NOM -->` sur des lignes seules : extraire la plage avec `sed -n`, puis retirer les deux lignes de repère
   - Substitue `{{TITLE_FOR_BANNER}}` (ou `{{IMG_URL}}` / `{{ALT_TEXT}}`) dans `header.html` via `sed`
   - Injecte les fragments et les placeholders scalaires (`{{TITLE}}`, `{{LANG_NOTICE}}`, `{{YEAR}}`) dans `assets/template.html` avec un script Python court
   - Vérifie qu'il ne reste **aucun** `{{…}}` dans le résultat — un placeholder oublié s'afficherait en clair dans l'email
   - Écrit le résultat final dans `/mnt/user-data/outputs/com-interne-<slug>.html`

⚠️ `content_en.html` contient uniquement des paragraphes / listes (comme `content_fr.html`), **jamais** de `<tr>` : le template le place déjà dans sa propre cellule. Le divider, lui, est un bloc `<tr>` complet, inséré tel quel.

**Exemple de squelette bash :**
```bash
SKILL=/mnt/skills/user/epi-com-interne/assets
OUT=/home/claude
snippet() { sed -n "/^<!-- BEGIN:$1 -->/,/^<!-- END:$1 -->/p" "$SKILL/banner-fallback-snippets.html" | sed '1d;$d'; }

# 1. Extraire les snippets (bandeau haut, divider si bilingue)
snippet HEADER  > $OUT/header.html
snippet DIVIDER > $OUT/divider.html      # mono-langue : : > $OUT/divider.html
[ -s $OUT/header.html ] || { echo "ERREUR : bandeau haut vide"; exit 1; }

# 2. Substituer le titre dans le bandeau haut
sed -i "s|{{TITLE_FOR_BANNER}}|IA Office Hours|g" $OUT/header.html

# 3. Assembler avec Python (plus robuste que sed pour multi-ligne)
python3 <<'PY'
import re
tpl = open('/mnt/skills/user/epi-com-interne/assets/template.html').read()
# Retirer l'en-tête de commentaires (il liste les placeholders : sinon ils seraient substitués là aussi)
tpl = re.sub(r'\A\s*<!--.*?-->\s*', '', tpl, count=1, flags=re.S)
subs = {
  '{{TITLE}}': 'IA Office Hours',
  '{{HEADER_BANNER}}': open('/home/claude/header.html').read(),
  '{{CONTENT_FR}}': open('/home/claude/content_fr.html').read(),
  '{{LANG_DIVIDER}}': open('/home/claude/divider.html').read(),
  '{{CONTENT_EN}}': open('/home/claude/content_en.html').read(),
  '{{LANG_NOTICE}}': '🇬🇧 English version below',
  '{{SIGNATURE}}': open('/home/claude/signature.html').read(),
  '{{YEAR}}': '2026',
}
for k,v in subs.items(): tpl = tpl.replace(k, v)
reste = re.findall(r'\{\{[A-Z_]+\}\}', tpl)
assert not reste, f'placeholders non substitués : {reste}'
open('/mnt/user-data/outputs/com-interne-ia-office-hours.html','w').write(tpl)
PY
```

**Ce que Claude ne doit JAMAIS faire :**
- ❌ Appeler `create_file` avec le HTML final complet (force la régénération des blobs base64)
- ❌ Copier-coller le contenu de `banner-fallback-snippets.html` dans un appel d'outil
- ❌ Réécrire le data URI `data:image/png;base64,...` même partiellement

**Ce que Claude doit faire :**
- ✅ Générer uniquement les fragments éditoriaux (texte FR/EN, signature) via `create_file`
- ✅ Assembler le HTML final 100% via `bash_tool` (cat/sed/python avec heredoc)
- ✅ Les blobs base64 restent dans les fichiers source, jamais dans le contexte de Claude

### Étape 4 — Sauvegarder et présenter

Le fichier final est déjà dans `/mnt/user-data/outputs/com-interne-<slug>.html` à la fin de l'étape 3. Appeler `present_files` avec ce chemin. Donner un résumé court en une ligne (« Email prêt — colle-le dans Gmail ou ouvre-le dans un navigateur pour vérifier le rendu »).

## Bandeaux fallback

Quand l'utilisateur ne fournit pas d'image pour un bandeau, générer un bloc HTML/CSS branded Epiconcept.

### Règle de couleurs (à respecter strictement)

| Zone | Couleur de fond | Pourquoi |
|---|---|---|
| **Bandeau haut** | Turquoise `#4BCDDB` uniforme | Identité visuelle Epiconcept |
| **Corps du mail** | Blanc `#ffffff` | Lisibilité, pas de turquoise dans le corps |
| **Footer légal** | Blanc `#ffffff`, sous un trait `#e5e5e5` | Texte 11px gris `#999999`, discret mais lisible |

⚠️ Le turquoise est **réservé au bandeau haut**. Ne JAMAIS l'utiliser comme fond pour le corps du mail, ni pour des blocs intermédiaires, ni pour un bandeau en bas. Pas de dégradé turquoise→bleu foncé dans le bandeau non plus — turquoise uniforme.

Le bleu foncé `#246589` est réservé au **texte** (H1 dans le corps), jamais comme fond.

### Bandeau du haut
- Hauteur : ~140-160px (padding 40px haut/bas)
- Fond : turquoise `#4BCDDB` uniforme
- Logo « e » blanc centré (56×56px, embarqué en base64)
- Titre du mail en blanc, 30px, bold, sous le logo
- Sous-titre minuscule « COMMUNICATION INTERNE » en blanc opacité 0.85, espacement-lettre 2px

### Footer légal (juste sous la signature)
- Dans la **même carte blanche** que le contenu, séparé de la signature par un trait gris fin `#e5e5e5` — pas de bandeau entre les deux, sinon le footer paraît détaché de l'email une fois collé dans Gmail
- Texte 11px gris `#999999`, centré
- Deux lignes : copyright année, mention salarié·e — pas d'adresse postale, pas d'email (il figure déjà dans la signature)
- Doit rester discret — c'est de la mention légale, pas du contenu

**Pourquoi HTML/CSS et pas SVG inline ?** Gmail strippe ou ignore les `<svg>` inline. Un `<table>` avec couleur de fond + image PNG du logo en base64 est universellement rendu dans Gmail (web + iOS + Android).

### Logo Epiconcept embarqué

Le snippet fallback intègre le logo « e » Epiconcept en **base64 (data URI)** — l'HTML reste self-contained, aucune dépendance externe. La version utilisée dans le bandeau haut est :

- `logo-e-white.png` (80×80, fond transparent, silhouette blanche) → bandeau **haut** (fond turquoise)

La version `logo-e-turquoise.png` n'est plus utilisée dans le rendu actuel (gardée comme actif au cas où un bandeau sur fond blanc/sombre serait nécessaire dans le futur).

Si l'utilisateur fournit sa propre image de bandeau, utiliser le snippet `HEADER_IMG` (`<img src="{{IMG_URL}}">`) — son image remplace alors entièrement le bandeau branded. Dans ce cas, vérifier qu'elle fait 600px de large minimum et qu'elle a un alt-text descriptif.

Voir `assets/banner-fallback-snippets.html` pour tous les snippets prêts à l'emploi.

## Compatibilité Gmail

Cible primaire : **Gmail (Google Workspace Epiconcept)** — web, iOS, Android. Quelques règles à respecter dans le HTML produit :

- **Tables pour le layout** (pas de flexbox/grid CSS) — Gmail strippe certains styles modernes
- **Styles inline uniquement** (`style="…"`) pour le texte et les couleurs — Gmail strippe `<style>` en `<head>` quand on colle l'HTML, et applique des transformations agressives sur le markup
- **Largeur maximale** : 600px (`max-width:600px`) pour le conteneur principal — Gmail web rend correctement à cette largeur
- **Poids total < 102 Ko** : au-delà, Gmail tronque le message et affiche « [Message clipped] ». Les logos en base64 + le contenu typique restent bien sous ce seuil ; vérifier si on ajoute beaucoup d'images
- **Police** : `'Source Sans Pro', 'Segoe UI', Arial, sans-serif` — Source Sans Pro n'est pas web-safe email, Gmail tombera sur Arial (rendu attendu, propre)
- **Couleurs** en hex 6 caractères, jamais en `rgb()` ou variables CSS — Gmail les ignore
- **Images** : `alt` toujours présent, `width`/`height` en attributs HTML (pas seulement en CSS) — Gmail tronque les images sans dimensions explicites
- **Data URIs (base64)** : OK pour les logos (Gmail les rend) — c'est pour ça que les logos sont embarqués comme tel dans les snippets fallback
- **Pas de JS**, pas de `<form>`, pas de `<iframe>` — Gmail les strippe ou refuse l'envoi

## Conventions de sujet (subject line)

Reprendre les patterns observés dans les exemples :

- Emoji thématique en début (🌍 / 🎉 / 🧑‍🏫 / 📢 …)
- Préfixe `[GROUPE]` quand l'émetteur est un groupe de travail (ex. `[SAM ENVIRONNEMENT]`, `[SAM Social]`, `[DSI]`)
- Mention `RAPPEL` / `REMINDER` en majuscules pour les relances
- Titre court et orienté action (« Calculez votre empreinte CARBONE » plutôt que « À propos de l'empreinte carbone »)

**Exemples :**
- `🌍 [SAM ENVIRONNEMENT] RAPPEL Calculez votre empreinte CARBONE !`
- `🎉 Epiconcept 30 ans – Thème soirée : Année 90 !`
- `🧑‍🏫 [SAM Social] REMINDER Register for a workshop "Diversity, equity and inclusion"`

## Fichiers de référence

- `references/editorial-patterns.md` — ton, structure, signature, conventions de sujet détaillées
- `assets/template.html` — template HTML autonome avec placeholders
- `assets/banner-fallback-snippets.html` — snippets `HEADER`, `HEADER_IMG` et `DIVIDER`, encadrés par des repères `BEGIN:` / `END:` (logo déjà embarqué en base64)
- `assets/logo-e-white.png` — logo « e » Epiconcept blanc, fond transparent (bandeau haut)
- `assets/logo-e-turquoise.png` — logo « e » Epiconcept turquoise, fond transparent (réserve, non utilisé au rendu)
- `assets/logo-e-original.png` — logo original turquoise sur fond noir (référence, peu utilisé)
