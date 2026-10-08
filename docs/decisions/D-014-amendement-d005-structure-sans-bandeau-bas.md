---
id: D-014
title: "Amendement D-005 — structure en cinq blocs : ni preheader ni bandeau bas, footer rattaché à la signature"
status: accepted
type: amendement
date: 2026-10-08
amends: D-005
supersedes: []
amended_by: []
sources:
  - D-005
  - D-007
  - D-011
patterns:
  - livrable-verifie-sur-sa-cible-reelle
---

## Déclencheur de l'amendement

> « déjà son affichage lors que je fais le copier-coller dans l'email
> n'est pas parfaite / Le bandeau en bas n'est pas nécessaire / La
> phrase "Copyright © 2026 EPICONCEPT / Vous recevez cet email en tant
> que salarié·e d'Epiconcept / You are receiving this email as an
> Epiconcept staff member." reste en déhors »

puis, sur la liste de propositions qui a suivi :

> « applique 1-5. C'est non pour 6 et 7 »

Les propositions 1 à 5 retenues : (1) cellule propre pour le contenu
EN, (2) extraction des snippets par repères, (3) suppression du bandeau
bas, (4) footer rattaché à la signature, (5) suppression du preheader
« View this email in your browser ».

Puis, à la vue d'un email d'exemple produit avec ces modifications :

> « enleve les deux dernières lignes du modèle "Epiconcept · 25, rue
> Titon · Paris 75011 · France / sam-environnement@epiconcept.fr" »

Deux défauts ont été **constatés** en assemblant un email de test et en
l'inspectant dans un navigateur, le 2026-10-08 :

- `{{CONTENT_EN}}` était placé directement entre deux `<tr>`
  (`template.html:70` avant amendement), sans cellule. Le navigateur
  sort ce contenu du tableau et l'affiche **au-dessus** de l'email, hors
  du conteneur 600px : en bilingue, la version anglaise apparaissait
  avant le bandeau haut.
- La commande d'extraction donnée en exemple (`SKILL.md:80` avant
  amendement) arrêtait sa plage `awk` sur la ligne `<!-- ====` qui
  **ferme l'en-tête du snippet** (ligne 26), et produisait un bandeau
  vide. La condition 5 de D-005 décrivait la fragilité ; elle était en
  fait déjà réalisée.

## Modification actée

### Axe 3 de D-005 (impacté) — structure verticale

**Avant** : sept blocs — preheader → bandeau haut → titre (+ mention
bilingue) → contenu FR → *(divider + contenu EN)* → signature → bandeau
bas → footer légal. Les deux bandeaux encadrent le contenu (« bookend
visuel »), le footer est après le bandeau bas.

**Après** : cinq blocs — bandeau haut → titre (+ mention bilingue) →
contenu FR → *(divider + contenu EN)* → signature → footer légal. Le
footer suit directement la signature, **dans la même carte blanche**,
séparé par un trait gris fin (`#e5e5e5`).

**Justification du changement** : le bandeau bas « fermait » l'email ;
une fois collé dans Gmail, le footer placé après lui se lisait comme
extérieur au message — c'est le symptôme rapporté. Le preheader
renvoyait à une archive web qui n'existe pas (`{{VIEW_URL}}` valait `#`,
divergence connue n° 5) : un lien mort en tête de chaque email collé.

### Axe 2 de D-005 (impacté) — contrat des placeholders

**Avant** : onze placeholders, dont `{{VIEW_URL}}`,
`{{FOOTER_BANNER}}` et `{{CONTACT_EMAIL}}`.

**Après** : huit — `{{VIEW_URL}}`, `{{FOOTER_BANNER}}` et
`{{CONTACT_EMAIL}}` supprimés (ce dernier n'apparaissait que dans le
footer ; l'email de contact reste demandé au cadrage et figure dans la
signature).
`{{CONTENT_EN}}` est désormais dans sa propre cellule `<td>` : il reçoit
des paragraphes, jamais de `<tr>`. `{{LANG_DIVIDER}}` reste un bloc
`<tr>` complet. Incrément **MAJEUR** au sens du `CHANGELOG.md`.

### Axe 4 de D-005 (impacté) — découpage des snippets

**Avant** : extraction par plages `awk` entre commentaires délimiteurs,
garde `NR>30` codée en dur ; snippets bandeau haut, bandeau bas, deux
variantes image, divider.

**Après** : chaque snippet est encadré par deux repères
`<!-- BEGIN:NOM -->` / `<!-- END:NOM -->` sur des lignes seules, et
s'extrait par `sed -n` sur ces repères. Plus de numéro de ligne codé en
dur : l'ordre des snippets dans le fichier devient indifférent. Restent
`HEADER`, `HEADER_IMG` et `DIVIDER`.

L'assemblage Python de `SKILL.md` retire en outre l'en-tête de
commentaires du template avant substitution (il liste les
placeholders : sans cela, le bandeau et son blob étaient dupliqués dans
le commentaire), et échoue s'il reste un `{{…}}`.

### Axe 5 de D-005 (impacté) — contenu du footer légal

**Avant** : copyright, mention « en tant que salarié·e d'Epiconcept »,
adresse postale, en dur ; année et email de contact variables.

**Après** : **deux lignes** — copyright et mention salarié·e. Adresse
postale et email de contact retirés. Le principe de l'axe est
préservé : ces mentions restent en dur, seule l'année varie.

**Justification du changement** : demande de l'opérateur. L'email de
contact faisait doublon avec la signature, juste au-dessus.

## Sections D-005 impactées vs préservées

- **Impacté** : axes 2, 3, 4 et 5 (cf. ci-dessus).
- **Préservé** : axe 1 (forme dans un fichier à substituer) ; le
  principe de l'axe 5 (mentions légales en dur, non paramétrées).
- **Conditions de légitimité** : condition 3 se lit « les mêmes cinq
  blocs » ; condition 5 devient « les repères `BEGIN:` / `END:` de
  chaque snippet sont présents et uniques » ; conditions 1, 2 et 4
  inchangées.

## Conséquences

- `skill/assets/template.html` : lignes preheader et bandeau bas
  supprimées ; cellule pour `{{CONTENT_EN}}` ; footer dans la carte
  (11px `#999999`, trait `#e5e5e5`) ; `bgcolor` en attribut en plus du
  style sur le conteneur et les cellules, que le collage dans Gmail
  conserve mieux ; footer réduit à deux lignes ; en-tête de
  commentaires à jour (huit placeholders).
- `skill/references/editorial-patterns.md` §« Footer standard » :
  adresse postale retirée.
- `skill/assets/banner-fallback-snippets.html` : bandeau bas et
  variante image bas supprimés ; repères `BEGIN:` / `END:` ajoutés. Le
  blob du bandeau haut est inchangé (vérifié par empreinte SHA-1).
- `skill/SKILL.md` : liste des blocs, question de cadrage sur le
  bandeau bas retirée (cinq questions au lieu de six), procédure et
  squelette d'assemblage réécrits, inventaire des fichiers corrigé.
- Couleurs : cf. [D-015]. Formulation de la `description` : cf.
  [D-016].
- Vérification : emails de test mono-langue et bilingue assemblés selon
  la nouvelle procédure et inspectés dans un navigateur — tous les
  blocs dans le conteneur, aucun placeholder résiduel. **Le rendu après
  collage dans Gmail n'a pas été vérifié** : c'est le contrôle à faire
  avant release.

## Sources

Internes : [D-005] (axes 2 à 5), [D-007] (assemblage par le shell,
inchangé), [D-011] (divider et contenu EN), [D-015], [D-016].

## Minutes de décision

**Q1 (périmètre)** : *« fais des proposition de modification »* →
sept propositions soumises ; **1 à 5 retenues, 6 et 7 refusées**
(consigne de collage dans `SKILL.md`, tests empiriques).

**Q2 (forme du séparateur de footer)** : trait gris fin ou bande gris
très clair proposés ; pas de réponse explicite de l'opérateur → trait
gris fin, l'option présentée en premier. À confirmer.

**Q3 (contenu du footer)** : *« enleve les deux dernières lignes du
modèle »* → **adresse postale et email de contact retirés** du
footer ; l'email de contact reste dans la signature.
