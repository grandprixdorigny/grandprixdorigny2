# Grand Prix Dorigny — modèle de site (v2)

Site public sur GitHub Pages + application web Google Apps Script + formulaire et feuille Google.
La page GitHub ne fait qu'afficher l'application Apps Script dans un cadre ; tout le reste vit dans le script.

```
/                 site GitHub Pages
  index.html      cadre qui affiche l'application (ne pas modifier)
  config.js       ← URL /exec du script de l'édition
  meteo/          page météo (liens relatifs)
  affiche.jpg     affiche de l'édition
/apps-script      code du script (à copier dans un projet Apps Script ou à pousser avec clasp)
  config.gs       ← réglages de l'édition (date, contact, places, listes d'envoi)
  Code.gs         main() : crée formulaire + feuille + déclencheur, mémorise les ID et le code admin
  webapp.gs       serveur de la page, désinscription, mode admin
  controle.gs     contrôles des inscriptions (doublons, erreurs)
  annonce.gs      envois groupés (annonce, lien WhatsApp)
  index.html      la page du site
```

## Nouvelle édition (ou autre club) en 8 étapes

1. **GitHub** : créer un dépôt à partir de celui-ci (« Use this template »), puis Settings → Pages → branche `main`, dossier `/`.
   Le site sera à `https://<compte>.github.io/<nom-du-dépôt>/`.
2. **Apps Script** : créer un projet vide sur script.google.com (nommer « GPD <année> »).
   Copier les fichiers du dossier `apps-script/` (ou `clasp push`, voir plus bas).
3. Dans `config.gs` : régler `DATE_REGATE`, `GPD_ORGA`, `GPD_SITE` (adresse de l'étape 1, sans `/` final), `GPD_PLACES`.
   Laisser `GPD_WHATSAPP`, `ANNONCE_LISTE` et `WA_LISTE` vides tant que ce n'est pas la vraie édition.
4. Lancer **`main`** depuis l'éditeur et autoriser. Il crée le formulaire, la feuille de réponses, le déclencheur des doublons,
   enregistre l'ID de la feuille dans les Propriétés du script et génère un **code admin** (envoyé par e-mail et visible dans le journal).
5. **Déployer → Nouveau déploiement → Application Web** : Exécuter en tant que *Moi*, Accès *Tout le monde*. Copier l'URL `/exec`.
6. Coller cette URL dans `config.js` du dépôt GitHub. Remplacer `affiche.jpg`.
7. Tester : inscription, doublon, liste d'attente, désinscription, mode admin (sur téléphone et ordinateur).
8. Pour la vraie édition : retirer la ligne `noindex` de `index.html`, remplir `GPD_WHATSAPP` et les listes d'envoi.

Après chaque modification du script : **Déployer → Gérer les déploiements → crayon → Nouvelle version**
(l'URL `/exec` reste la même, `config.js` n'a pas à changer).

## Sécurité

- Le code admin et l'ID de la feuille sont dans **Paramètres du projet → Propriétés du script** (`CODE_ADMIN`, `SHEET_ID`, `SHEET_GID`), jamais dans GitHub.
- Après 5 codes admin faux en une heure, l'accès admin est bloqué une heure et l'organisateur reçoit une alerte.
- Les fonctions lancées à la main (`main`, envois, tests) refusent de tourner si elles sont appelées depuis la page.
- Ne jamais publier la feuille sur le Web ni la partager par lien.

## Option : versionner le script avec clasp

```
npm install -g @google/clasp
clasp login
cd apps-script
clasp create --type standalone --title "GPD 2027"   # ou clasp clone <scriptId> pour un projet existant
clasp push
```
