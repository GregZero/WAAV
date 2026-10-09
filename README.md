# 💬 WAAV — WhatsApp Archive Viewer

**Parcourez, fusionnez et recherchez vos archives de conversations WhatsApp, directement dans votre navigateur.**
Un seul fichier HTML, aucune installation, aucun compte, **aucune donnée envoyée sur Internet**.

![Conversation](docs/screenshot-conversation.png)

> Les captures utilisent une conversation fictive.

---

## ✨ Fonctionnalités

**Import**
- Import d'un ou plusieurs exports WhatsApp `.txt` (glisser-déposer ou sélection), y compris de très grosses archives (100 000+ messages)
- Fusion de plusieurs archives avec **détection des doublons** (surlignés pour contrôle visuel, suppression sur confirmation)
- Réimport des exports JSON de WAAV

**Lecture**
- Conversation unifiée avec bulles façon WhatsApp, côté gauche / droite / masqué par participant
- Regroupement **Année → Mois → Jour**, repliable (réglable : par Mois, Jour, Année ou aucun)
- Filtre de période : « Année 2025 », « Janvier 2025 » … « Décembre 2025 »
- Affichage progressif (les bulles ne sont créées qu'à l'ouverture d'un groupe) pour rester fluide

![Recherche](docs/screenshot-recherche.png)

**Recherche**
- Recherche par mot-clé avec surlignage, lancée avec Entrée ou en quittant le champ, effacée avec Échap ou le bouton ✕
- Avertissement (fenêtre de confirmation) quand le nombre de résultats risque de ralentir l'affichage — **seuils réglables** dans ⚙ Réglages

**Conservation**
- Les archives sont gardées dans le navigateur (IndexedDB) : à la réouverture, WAAV propose de **reprendre**, démarrer sans charger ou effacer
- Détection des anciennes bases laissées par des versions précédentes, suppression uniquement sur demande

**Export**
- JSON (réimportable), TXT au format WhatsApp (réimportable), SQLite (`.db`), MySQL (`.sql`, utf8mb4, importable dans phpMyAdmin)

---

## 🚀 Utilisation

### En ligne (GitHub Pages)
Ouvrez l'adresse de la page du projet : `https://gregzero.github.io/WAAV/`

### En local
1. Téléchargez [`index.html`](index.html) et **renommez-le `waav.html`**
2. Placez-le dans un dossier de votre choix et ouvrez-le avec votre navigateur
3. Glissez-y vos fichiers `.txt` exportés depuis WhatsApp

### Exporter une conversation depuis WhatsApp
Sur Android : dans la conversation, menu **⋮ › Plus › Exporter la discussion**. Sur iPhone : nom du contact en haut de la conversation › **Exporter la discussion**. Choisissez **Sans médias**, puis récupérez le fichier `.txt`.
Formats de date reconnus : `JJ/MM/AAAA, HH:MM - Expéditeur: message`, variantes avec « à HH:MM », format `[date]`, séparateurs `/`, `-`, `.`.

---

## 🔒 Confidentialité

- Vos conversations **ne quittent jamais votre ordinateur** : elles sont lues et stockées uniquement dans votre navigateur. Il n'y a pas de serveur.
- Deux ressources externes sont chargées : les **polices Google Fonts** (à l'ouverture de la page) et la bibliothèque **sql.js** depuis cdnjs (uniquement quand vous utilisez l'export SQLite). Aucune donnée de conversation n'y est transmise, mais ces services reçoivent l'adresse IP du visiteur. *(Les héberger soi-même est une évolution prévue.)*
- **Ne publiez jamais de vraies conversations** dans un dépôt public : le fichier `.gitignore` fourni exclut les formats courants (`.txt`, `.json`, `.db`, `.sql`).

---

## 💾 Stockage : à connaître

Les archives sont enregistrées **dans le navigateur de l'appareil**, pas dans le fichier HTML.

- **Vider les données du navigateur efface vos archives.** Faites de temps en temps un **Export → JSON**.
- **En usage local (`file://`), le navigateur lie les données au chemin complet du fichier** (c'est le cas de Firefox). Pour retrouver vos données d'une version à l'autre : gardez **le même nom (`waav.html`) et le même dossier**, et remplacez simplement le fichier par la nouvelle version. WAAV affiche dans ⚙ Réglages › « Stockage lié à » l'adresse utilisée et avertit si le nom diffère.
- **En ligne ou via un serveur à adresse fixe**, toutes les versions servies par la même adresse partagent le même stockage, quel que soit le nom du fichier.

---

## 🛠️ Technique

- HTML5, CSS3, JavaScript « vanilla », IndexedDB — un seul fichier, pas de dépendance de build
- Base IndexedDB `wa_archive` (stores `files` et `meta`) ; réglages dans le `localStorage`
- Navigateurs récents. Testé sur Firefox 115 ESR et sur Chromium ; les autres navigateurs modernes devraient fonctionner mais n'ont pas été vérifiés.

L'historique complet du développement (versions, demandes, corrections, choix techniques, fonctionnalités abandonnées) est dans **[HISTORIQUE.md](HISTORIQUE.md)**. Le changelog est aussi consultable dans l'application en cliquant sur le numéro de version.

---

## 🗺️ Feuille de route (v2.0)

- « Voir tout ce jour » depuis un résultat de recherche, navigation jour précédent / suivant
- Résumés et questions sur une conversation via l'API Claude
- Synchronisation MySQL (API PHP)
- Thème clair, mini-calendrier, marque-pages, affichage des médias
- Polices et bibliothèque SQLite hébergées avec l'application

---

## 🤝 Contribuer

Les suggestions et corrections sont les bienvenues : ouvrez une *issue* ou une *pull request*.
En contribuant, vous acceptez que votre contribution soit publiée sous la même licence (GPL-3.0-or-later).
Conseils pour contribuer : partir de la dernière version stable, un seul changement fonctionnel à la fois, vérifier la syntaxe JavaScript, mettre à jour le changelog intégré **et** `HISTORIQUE.md`.

---

## 👥 Crédits

- **Concept original :** R&R (Régori & Rabrielle)
- **Développement :** Claude (Anthropic)

## 📄 Licence

**[GNU GPL v3 ou ultérieure](LICENSE)** (`GPL-3.0-or-later`) — logiciel libre.

Vous pouvez utiliser, étudier, modifier et redistribuer WAAV, y compris l'héberger sur votre propre site. Si vous distribuez une version (modifiée ou non), elle doit rester sous la même licence, avec son code source disponible : le code de WAAV reste libre.

WAAV n'est ni affilié, ni approuvé par WhatsApp ou Meta. WhatsApp est une marque de Meta Platforms, Inc.
