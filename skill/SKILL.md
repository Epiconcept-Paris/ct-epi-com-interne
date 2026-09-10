---
name: epi-com-interne
description: "Met en forme une communication interne Epiconcept en email HTML (Gmail + prévisualisable navigateur) : bandeau haut, contenu mono/bilingue FR/EN, bandeau bas, footer. ⚠️ NE PAS déclencher automatiquement, même pour un email interne Epiconcept. Utiliser UNIQUEMENT dans l'un de ces deux cas : (a) l'utilisateur la nomme explicitement (« /epi-com-interne », « applique epi-com-interne », « com interne Epiconcept ») ; (b) Claude estime la skill utile : il ARRÊTE sa production, POSE la question en une phrase courte (ex. « Tu veux que je formate ça en com interne Epiconcept (email HTML branded pour Gmail) ? »), ATTEND la réponse, puis applique le choix. ⚠️ La question se pose AVANT de produire le livrable, jamais après. INTERDIT de produire un brouillon texte/markdown sans la skill puis de proposer de le retransformer en email branded avec epi-com-interne. Sans invocation explicite ni réponse positive, produire le contenu sans la mise en forme email branded."
---

# Communication interne Epiconcept — Email

Cette skill produit une communication interne Epiconcept au format **HTML autonome**, prête à coller dans **Gmail** et à prévisualiser dans un navigateur. Aucune dépendance Mailchimp. Cible email primaire : **Gmail (Google Workspace Epiconcept)**.

## Ce qu'il faut produire

Un fichier `.html` unique, self-contained, avec cette structure verticale (de haut en bas) :

1. **Preheader** — lien « View this email in your browser » (à droite, petit, gris)
2. **Bandeau image haut** — visuel thématique (fourni par l'utilisateur ou fallback HTML branded)
3. **Titre** — en bleu foncé Epiconcept, avec emoji thématique
4. **Corps** — version FR, et optionnellement version EN en dessous (séparée par un divider)
5. **Bandeau image bas** — visuel branded Epiconcept (fourni ou fallback)
6. **Signature** — équipe/groupe émetteur + email de contact
7. **Footer** — copyright, mention salarié·e Epiconcept, adresse postale

## Workflow

### Étape 1 — Interroger avant de rédiger

⚠️ **Avant toute rédaction**, poser ces questions à l'utilisateur via l'outil `ask_user_input_v0` (regrouper en un seul appel quand c'est possible) :

| Question | Pourquoi |
|---|---|
| **Sujet/titre court** (ex. « Calculez votre empreinte carbone ») | Sert au `<title>`, au preheader subject, et au H1 |
| **Émetteur** (ex. « Equipe SAM Environnement (Lore, Fabrice, Maud) » / « Epifun » / « DSI ») | Sert à la signature et au from-name implicite |
| **Email contact** (ex. `sam-environnement@epiconcept.fr`) | Lien cliquable dans la signature |
| **Bilingue FR/EN ?** (oui / non) | Si oui : produire les deux versions (FR puis EN, séparées par un trait + 🇬🇧) |
| **Bandeau du haut** (image fournie / fallback généré) | Si image : demander URL ou chemin local ; si fallback : générer un bandeau HTML/CSS branded |
| **Bandeau du bas** (image fournie / fallback généré) | Idem |

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
   - Copie `assets/template.html` vers `/home/claude/email.html`
   - Extrait les snippets `BANDEAU HAUT` et `BANDEAU BAS` de `assets/banner-fallback-snippets.html` vers des fichiers temporaires (`/home/claude/header.html`, `/home/claude/footer.html`)
   - Substitue `{{TITLE_FOR_BANNER}}` dans `header.html` via `sed`
   - Utilise `sed -e "/{{PLACEHOLDER}}/{r /home/claude/fragment.html" -e "d;}"` ou un script Python court pour injecter chaque fragment dans le template
   - Substitue les placeholders scalaires (`{{TITLE}}`, `{{CONTACT_EMAIL}}`, `{{YEAR}}`, `{{VIEW_URL}}`, `{{LANG_NOTICE}}`) via `sed`
   - Pour le divider FR/EN bilingue, extraire le bloc `DIVIDER FR / EN` de `banner-fallback-snippets.html` ou laisser vide si mono-langue
   - Copie le résultat final vers `/mnt/user-data/outputs/com-interne-<slug>.html`

**Exemple de squelette bash :**
```bash
SKILL=/mnt/skills/user/epi-com-interne/assets
OUT=/home/claude

# 1. Extraire les snippets bandeaux (entre les commentaires délimiteurs)
awk '/BANDEAU HAUT — fallback/,/^<!-- ====/{ if(/^<!-- ====/ && NR>30) exit; print }' "$SKILL/banner-fallback-snippets.html" \
  | grep -v '^<!--' > $OUT/header.html
# (idem pour bandeau bas et divider)

# 2. Substituer le titre dans le bandeau haut
sed -i "s|{{TITLE_FOR_BANNER}}|IA Office Hours|g" $OUT/header.html

# 3. Assembler avec Python (plus robuste que sed pour multi-ligne)
python3 <<'PY'
tpl = open('/mnt/skills/user/epi-com-interne/assets/template.html').read()
subs = {
  '{{TITLE}}': 'IA Office Hours',
  '{{VIEW_URL}}': '#',
  '{{HEADER_BANNER}}': open('/home/claude/header.html').read(),
  '{{FOOTER_BANNER}}': open('/home/claude/footer.html').read(),
  '{{CONTENT_FR}}': open('/home/claude/content_fr.html').read(),
  '{{CONTENT_EN}}': open('/home/claude/content_en.html').read(),
  '{{LANG_DIVIDER}}': open('/home/claude/divider.html').read(),
  '{{LANG_NOTICE}}': '🇬🇧 English version below',
  '{{SIGNATURE}}': open('/home/claude/signature.html').read(),
  '{{CONTACT_EMAIL}}': 'comite-tech@epiconcept.fr',
  '{{YEAR}}': '2026',
}
for k,v in subs.items(): tpl = tpl.replace(k, v)
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
| **Bandeau bas** | Turquoise `#4BCDDB` (même que le haut) | Symétrie haut/bas, bookend visuel |
| **Footer copyright** | Blanc `#ffffff` | Texte 10px gris très clair `#bbbbbb`, discret |

⚠️ Le turquoise est **réservé aux deux bandeaux**. Ne JAMAIS l'utiliser comme fond pour le corps du mail, ni pour des blocs intermédiaires. Pas de dégradé turquoise→bleu foncé dans les bandeaux non plus — turquoise uniforme.

Le bleu foncé `#246589` est réservé au **texte** (H1 dans le corps), jamais comme fond.

### Bandeau du haut
- Hauteur : ~140-160px (padding 40px haut/bas)
- Fond : turquoise `#4BCDDB` uniforme
- Logo « e » blanc centré (56×56px, embarqué en base64)
- Titre du mail en blanc, 30px, bold, sous le logo
- Sous-titre minuscule « COMMUNICATION INTERNE » en blanc opacité 0.85, espacement-lettre 2px

### Bandeau du bas
- Hauteur : ~100-120px (padding 28px haut/bas, plus compact que le haut)
- Fond : **même turquoise `#4BCDDB` que le haut** (symétrie)
- Logo « e » blanc centré (44×44px, plus petit que le haut)
- « Epiconcept » en blanc 17px bold
- « SMART HEALTH » en blanc opacité 0.85, espacement-lettre 3px

### Footer copyright (sous le bandeau bas)
- Fond blanc, texte 10px gris très clair `#bbbbbb`
- Lignes : copyright année, mention salarié·e, adresse postale, email contact
- Doit rester très discret — c'est de la mention légale, pas du contenu

**Pourquoi HTML/CSS et pas SVG inline ?** Gmail strippe ou ignore les `<svg>` inline. Un `<table>` avec couleur de fond + image PNG du logo en base64 est universellement rendu dans Gmail (web + iOS + Android).

### Logo Epiconcept embarqué

Les snippets fallback intègrent le logo « e » Epiconcept en **base64 (data URI)** — l'HTML reste self-contained, aucune dépendance externe. La version utilisée dans les deux bandeaux est :

- `logo-e-white.png` (80×80, fond transparent, silhouette blanche) → utilisée en bandeau **haut** ET **bas** (les deux ayant fond turquoise)

La version `logo-e-turquoise.png` n'est plus utilisée dans le rendu actuel (gardée comme actif au cas où un bandeau sur fond blanc/sombre serait nécessaire dans le futur).

Si l'utilisateur fournit sa propre image de bandeau, utiliser le snippet **variante** (`<img src="{{IMG_URL}}">`) — son image remplace alors entièrement le bandeau branded. Dans ce cas, vérifier qu'elle fait 600px de large minimum et qu'elle a un alt-text descriptif.

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
- `assets/banner-fallback-snippets.html` — snippets HTML/CSS pour bandeaux haut et bas (logos déjà embarqués en base64)
- `assets/logo-e-white.png` — logo « e » Epiconcept blanc, fond transparent (pour bandeau haut)
- `assets/logo-e-turquoise.png` — logo « e » Epiconcept turquoise, fond transparent (pour bandeau bas)
- `assets/logo-e-original.png` — logo original turquoise sur fond noir (référence, peu utilisé)
