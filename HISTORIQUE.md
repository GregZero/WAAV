# WAAV — Historique du projet

> Document de suivi du développement : versions, demandes, corrections, choix techniques et fonctionnalités abandonnées.
> Retour au [README](README.md).

---

## Informations générales

| Champ | Valeur |
|-------|--------|
| Nom de l'application | WAAV — WhatsApp Archive Viewer |
| Type | Application web statique (un seul fichier HTML autonome, aucune donnée envoyée à un serveur) |
| Technologies | HTML5, CSS3, JavaScript vanilla, IndexedDB |
| Concept original | R&R (Régori & Rabrielle) |
| Développement | Claude (Anthropic) |
| Licence | GPL-3.0-or-later |
| Démarrage du projet | Septembre 2026 |
| Version stable actuelle | v2.1.0 (v2.1.1 en cours de validation) |

---

## Architecture technique

### Stockage
- **v1.0 – v1.7** : `localStorage` (limité à ~5 MB)
- **v1.8+** : `IndexedDB` (base `wa_archive`, stores `files` + `meta`, illimité)
- **Clés obsolètes** : `wa_archive_v2`, `wa_v5`, `wa_v6`

### Rendu des messages
- **v1.0 – v1.7** : Rendu DOM complet à chaque chargement
- **v1.8+** : Rendu lazy par jour (bulles créées uniquement à l'ouverture du groupe)

### Parsing supporté
- Format WhatsApp : `DD/MM/YYYY, HH:MM - Expéditeur: message`
- Variantes : `à HH:MM`, format `[date]`, séparateurs `/`, `-`, `.`
- **v1.8.8+** : Import JSON (exports WAAV) également supporté

---

## Historique des versions

---

### v1.0 — Version initiale
**Fonctionnalités :**
- Import fichier `.txt` WhatsApp par glisser-déposer ou sélection
- Affichage des messages avec bulles style WhatsApp (gauche/droite)
- Regroupement par jour et mois avec sections dépliables
- Recherche par mot-clé avec surlignage en jaune
- Sauvegarde localStorage
- Export JSON

**Bugs connus :**
- Affichage séparé par contact au lieu d'une conversation unifiée → corrigé v1.1

---

### v1.1 — Conversation unifiée & période
**Demandes :**
- Afficher tous les participants dans une seule conversation
- Ajouter un filtre temporel par mois/année

**Implémentations :**
- Conversation unifiée tous participants
- Sélecteur de période dynamique
- Légende des participants cliquable (masquer/afficher)

---

### v1.2 — Multi-import
**Demandes :**
- Importer plusieurs archives, concaténer, supprimer les doublons
- Numéro de version dans l'application

**Implémentations :**
- Import multiple `.txt`
- Liste des archives avec retrait individuel
- Déduplication automatique
- Badge version

**Bugs connus :**
- Faux doublons au premier import → corrigé v1.3

---

### v1.3 — Doublons visuels
**Demandes :**
- Voir les doublons pour contrôle visuel
- Bouton pour les supprimer après vérification

**Implémentations :**
- Détection doublons cross-fichiers
- Bannière déduplication sidebar
- Vue doublons filtrée
- Bouton "Supprimer"

**Corrections :**
- Faux doublons au premier import (v1.2) → corrigé

---

### v1.4 — Collapse, tooltip, merge
**Demandes :**
- Bouton "Tout empiler / dépiler"
- Bulle de date au survol
- Correction bug merge

**Implémentations :**
- Bouton empiler/dépiler
- Tooltip date CSS `::after`
- Versionnage X.Y.Z

**Note :** Bug merge tenté — corrigé définitivement en v1.5

---

### v1.5 — Correctifs import définitifs
**Demandes :**
- Corriger le bug merge (Ajouter remplaçait au lieu d'ajouter)

**Implémentations :**
- Input file unique piloté par `_importMode`
- Liste fichiers mise à jour en temps réel
- Doublons encadrés orange + décompte stats

**Corrections :**
- Bug fondamental input file dupliqué → résolu

---

### v1.6 — Options avancées
**Demandes :**
- Case à cocher "Tout empiler par défaut"
- Bouton masquer/afficher marquage orange sans supprimer
- Badge version cliquable → changelog

**Implémentations :**
- Sélecteur "Empiler par" (Jour/Mois/Année)
- Bouton "🟠 Masquer doublons"
- Modal changelog complet

---

### v1.7 — Structure hiérarchique
**Demandes :**
- Empilage par Jour / Mois / Année
- Sélecteur période : années ET mois
- Clic participant → inverser côté bulles

**Implémentations :**
- Hiérarchie Année → Mois → Jour
- Sélecteur période avec années séparées
- Chips participants : cycle 3 états (gauche/droite/masqué)
- Dot du chip se déplace physiquement dans le DOM

---

### v1.8 — Performances gros volumes
**Demandes :**
- Fluidité sur 100 000+ messages

**Implémentations :**
- Rendu lazy par jour
- IndexedDB remplace localStorage
- Index de période pré-calculé O(1)
- Parsing non-bloquant

---

### v1.8.1 — Micro-corrections
**Demandes :**
- Renommer → WAAV
- Supprimer bouton "Parcourir" natif
- Recherche après fin de saisie uniquement
- Versionnage 3 chiffres

**Implémentations :**
- Nom WAAV + sous-titre WhatsApp Archive Viewer
- Input file masqué
- Debounce 600ms + Entrée
- Versionnage X.Y.Z

---

### v1.8.2 — Panneau gauche masquable
**Demandes :**
- Masquer/afficher le panneau gauche (mobile et desktop)

**Implémentations :**
- Bouton ☰ dans le header
- Desktop : slide CSS `body.sidebar-hidden`
- Mobile : tiroir avec overlay

**Corrections :**
- `openChangelog()` manquant → restauré
- Sélecteur CSS sibling → remplacé par classes sur `body`

---

### v1.8.3 — Nettoyage UI
**Demandes :**
- Supprimer l'encart conversation (avatars + aperçu) dans le panneau gauche

**Implémentations :**
- Suppression `#conv-panel` et `renderConvPanel()`
- Chips participants cycle 3 états affiné
- Tooltips corrigés

---

### v1.8.4 — Repliage & recherche
**Demandes :**
- Bouton vert "Choisir un fichier" restauré
- Bouton "↑ replier" en bas de chaque groupe
- Préserver état des groupes lors recherche/changement participant
- Recherche uniquement sur Enter ou blur

**Implémentations :**
- Bouton vert drop-overlay restauré
- Bouton replier en bas de chaque groupe jour/mois/année
- `captureCollapseState()` + `data-gkey`
- `preserveState=true` sur re-rendus
- Recherche : Enter ou blur uniquement
- Touche Échap efface et réinitialise
- Auto-dépliage des jours avec résultats
- Scroll vers premier résultat

---

### v1.8.5 — Aide utilisateur
**Demandes :**
- Icône 💬 cliquable → aide complète

**Implémentations :**
- Modal d'aide (8 sections)
- Bouton Empiler/Dépiler limité au niveau actif
- "Empiler par" appliqué immédiatement

---

### v1.8.6 — Démarrage & doublons
**Demandes :**
- Éviter blocage navigateur au rechargement gros volumes
- Choix au démarrage quand archives sauvegardées
- Bouton "Supprimer les doublons" plus visible

**Implémentations :**
- "Empiler par" = Jour par défaut
- Modal de reprise : Reprendre / Démarrer sans charger / Effacer
- Seules les métadonnées lues en premier
- Bannière doublons en bas sidebar
- Bouton "🗑 Supprimer les doublons"

**Corrections :**
- Bug critique : `confirmDedup()` supprimait TOUTES les occurrences y compris la première → corrigé avec `seen Set`

---

### v1.8.7 — Exports multi-formats
**Demandes :**
- Export TXT format WhatsApp réimportable
- Export SQLite (.db)
- Export MySQL (.sql) pour phpMyAdmin

**Implémentations :**
- Menu déroulant Export : JSON / TXT / SQLite / MySQL
- TXT : format `JJ/MM/AAAA, HH:MM - Expéditeur: message`
- SQLite : via `sql.js` CDN à la demande
- MySQL : `.sql` utf8mb4, INSERT par lots de 500

---

### v1.8.8 — Nettoyage final & import JSON ⭐ VERSION STABLE DE RÉFÉRENCE
**Demandes :**
- Export dans le panneau gauche
- Stats du bandeau bas → panneau gauche
- Supprimer bouton "Ajouter" (Importer suffit)
- Import JSON supporté
- Améliorer mise en forme de l'aide

**Implémentations :**
- Export → bouton dans panneau gauche (à côté d'Importer/Effacer)
- Stats en bas du panneau gauche, bandeau bas supprimé
- Bouton "Ajouter" supprimé, Importer = mode merge
- `parseJSON()` : import fichiers `.json` WAAV
- Modal d'aide redessinée (sections séparées, chips visuels)

**Note technique :** Version stable de référence. Toutes les versions suivantes partent de cette base.

---

### v1.9 — (Abandonnée — instable)
**Statut :** ❌ Abandonnée

**Raison :** Accumulation de patches sur un fichier déjà patché plusieurs fois. Conflits IndexedDB entre v1.9 et v1.9.1 provoquant un écran noir au chargement. Les fonctionnalités ont été réintégrées proprement en v1.9.1 puis v1.9.2/v1.9.3 depuis la base v1.8.8.

**Fonctionnalités tentées (reportées sur versions suivantes) :**
- "Empiler par" Mois par défaut → intégré v1.9.2+
- Confirmation avant affichage si > 50 résultats → intégré v1.9.3
- Easter egg R&R → intégré v1.9.3
- Crédits dans l'aide → intégré v1.9.3
- Nettoyage données versions précédentes → intégré v1.9.3
- Bouton "Voir tout ce jour" en recherche → ❌ non fonctionnel, abandonné pour l'instant
- Navigation jour précédent/suivant → ❌ non fonctionnel, abandonné pour l'instant

---

### v1.9.1 — (Abandonnée — instable)
**Statut :** ❌ Abandonnée

**Raison :** Même problème que v1.9 — écran noir au démarrage lié à des conflits IndexedDB. Reconstruit depuis v1.8.8 mais instable.

---

### v1.9.2 — Recherche contexte (partiel)
**Base :** v1.8.8 stable

**Demandes :**
- Bouton "Voir tout ce jour" lors d'une recherche
- Navigation jour précédent/suivant

**Implémentations :**
- CSS `.ctx-btn` et `.day-nav-bar` ajoutés
- `loadDayGroup()` modifié : en mode recherche, affiche les bulles correspondantes + bouton "📅 Voir tout ce jour"
- `expandFullDay()` : charge tous les messages du jour + barre de navigation
- `expandAdjacentDay()` : ouvre le jour calendaire adjacent complet

**Statut fonctionnel :** ⚠️ Partiellement fonctionnel — les fonctionnalités "Voir tout ce jour" et navigation jour sont présentes dans le code mais ne fonctionnent pas correctement en pratique. Abandonné pour cette itération.

---

### v1.9.3 — Corrections & easter egg ✅ TERMINÉE
**Base :** v1.9.2

**Demandes :**
- Empiler par Mois par défaut (était perdu depuis v1.8.8)
- Confirmation recherche si > 50 résultats (était perdu depuis v1.9)
- Easter egg R&R avec une oie qui court après une tortue
- Abandon définitif des fonctionnalités "Voir tout ce jour" et navigation jour (non fonctionnelles)

**Implémentations :**
- `#collapse-level` : option Mois sélectionnée par défaut (ordre : Mois / Jour / Année / Aucun)
- `applySearch()` : `confirm()` si plus de 50 résultats (seuil fixe remplacé en v1.9.4 par des seuils réglables) ; en cas de refus, la recherche précédente est restaurée ; garde `_searchBusy` pour éviter un double déclenchement Entrée + blur
- Easter egg R&R : `startEasterEgg()` — oie 🪿 qui court après une tortue 🐢 (animation CSS ~8 s, non bloquante). Déclenché en cliquant sur « R&R » dans les crédits de l'aide, ou en recherchant « R&R »
- Aide : section « ✨ Crédits » (concept R&R, développement Claude, version) ; mention de la confirmation > 50 résultats ; mention obsolète du bouton « Ajouter » corrigée
- `cleanupLegacyData()` au démarrage : suppression des clés localStorage obsolètes `wa_archive_v2`, `wa_v5`, `wa_v6` (et variantes `wa_v*` / `wa_archive_v*`)
- Retrait complet du code « Voir tout ce jour » et navigation jour : CSS `.ctx-btn` / `.day-nav-*`, `expandFullDay()`, `expandAdjacentDay()`, `_allMsgs` ; `loadDayGroup()` simplifié (un seul chemin, bouton « replier » toujours présent)
- Titre, badge et changelog in-app passés en v1.9.3

**Vérifications :** `new Function(js)` OK ; plus aucune référence aux fonctions supprimées ; version cohérente (titre, badge, changelog).

**Statut :** ✅ Terminée — à valider par test manuel (rechargement depuis IndexedDB, recherche > 50 résultats, easter egg)

---

### v1.9.4 — Réinitialisation recherche, seuils & réglages ⭐ VERSION STABLE (validée)
**Base :** v1.9.3

**Demandes :**
- Bouton dans le style actuel pour réinitialiser la recherche (mêmes actions que Échap)

**Implémentations :**
- Bouton ✕ rond vert (`#search-clear`) à droite du champ de recherche, visible uniquement quand le champ contient du texte
- `clearSearch()` : fonction unique partagée par Échap et le bouton (vide le champ, réinitialise `searchQuery`, recalcule et re-rend)
- `onmousedown=preventDefault` sur le bouton : évite que le blur du champ relance la recherche avant l'effacement
- `updateSearchClear()` synchronise l'affichage du bouton (saisie, refus de confirmation > 50 résultats, Effacer)
- Aide et changelog in-app mis à jour

**Demande complémentaire (même version) :**
- Seuils d'avertissement de recherche réglables, d'après les observations de performance : ≤ 500 résultats fluide, ~1 000 un peu plus long, > 5 000 long

**Implémentations (complément) :**
- Remplacement du seuil fixe (50) par deux seuils : 🟡 avertissement (défaut **1 000**) et 🔴 avertissement fort (défaut **5 000**, message plus insistant suggérant de restreindre période/mot-clé)
- Réglages dans la fenêtre « ⚙ Réglages » (voir ci-dessous) : deux champs numériques + bouton « Valeurs par défaut » ; 0 = seuil désactivé
- Mémorisation dans `localStorage` (clé `waav_thresholds`) — volontairement hors IndexedDB car `clearStorage()` vide le store `meta` ; non touchée par `cleanupLegacyData()`
- Cohérence : seuil fort ≥ seuil d'avertissement (corrigé automatiquement) ; valeurs invalides ignorées
- `searchWarningMessage()` isole la logique de seuil ; `applySearch()` l'utilise
- Aide et changelog mis à jour

**Demande complémentaire 2 (même version) :**
- Bouton Réglages (icône engrenage) à côté du titre et de la version, pour accueillir les futurs réglages

**Implémentations (complément 2) :**
- Bouton engrenage `.settings-btn` dans l'en-tête du panneau gauche (aligné à droite, légère rotation au survol)
- Fenêtre `#settings-modal` (mêmes styles que l'aide, fermeture par « Fermer » ou clic en dehors), organisée en sections `help-section` : pour ajouter un réglage, ajouter une section
- Section actuelle : « 🔍 Recherche — seuils d'avertissement » (déplacée depuis le panneau gauche, qui est de nouveau allégé)
- `openSettings()` / `closeSettings()` ; aide et changelog mis à jour

**Statut :** ✅ Terminée — à valider par test manuel

---

### v1.9.5 — Détection des anciennes bases à l'ouverture ✅ TERMINÉE
**Base :** v1.9.4 (stable)

**Demande :**
- Nettoyer / détecter les anciennes bases sans supprimer les données des anciennes versions 1.X
- Précision (2e message) : le nettoyage « à la fermeture » ne peut pas fonctionner (fermeture brutale du navigateur) ; il faut détecter à l'ouverture. Constat : seules les bases créées avec exactement la même version étaient détectées (ex. une page v1.9.5 ne voit pas les données laissées par une v1.9.4)

**Constat technique :** un navigateur isole le stockage (IndexedDB, localStorage) par origine ; pour des fichiers `file://`, selon le navigateur, chaque fichier HTML (nom/dossier différents, donc chaque version) peut avoir son propre stockage, invisible depuis un autre fichier. Aucun code ne peut contourner cette isolation : une page ne peut détecter que ce qui se trouve dans SON stockage.

**Implémentations :**
- Abandon du nettoyage à la fermeture (`pagehide`) et du nettoyage au démarrage de la v1.9.3 : **aucune suppression automatique**
- `scanLegacyData()` à l'ouverture : détecte les clés localStorage obsolètes (`wa_archive_v2`, `wa_v5`, `wa_v6`, `wa_v*`, `wa_archive_v*`, avec leur taille) et toutes les bases IndexedDB `wa_*` autres que `wa_archive` (avec leur version de schéma), quelle que soit la version de WAAV qui les a créées
- Signature des archives : `appVersion` + `savedAt` écrits dans `meta` à chaque sauvegarde ; la fenêtre de reprise indique « enregistrées avec WAAV vX.Y.Z le … (vous êtes en v1.9.5) » ou « version antérieure à 1.9.5 » pour les données non signées
- Fenêtre de démarrage : affichée aussi s'il n'y a que d'anciennes bases, ou si `wa_archive` ne peut pas être ouverte (schéma d'une autre version) ; encart orange listant les anciennes bases avec bouton « Supprimer ces anciennes bases » (confirmation détaillée, irréversible)
- ⚙ Réglages : section « 🗂 Données — anciennes bases » (état, « Rechercher à nouveau », « Supprimer les anciennes bases ») et section « 📍 Stockage lié à » (adresse du fichier + explication de l'isolation et de la procédure Export JSON → Importer)
- Jamais concernés par la suppression : base `wa_archive`, clés `waav_*`, tout ce qui n'a pas le préfixe `wa_`
- Aide et changelog mis à jour

**Vérifications :** `new Function(js)` OK ; équilibre des `<div>` OK ; simulation (stubs) de la détection et de la suppression confirmée : `wa_archive`, `waav_*` et bases étrangères conservées.

**Limites connues :** impossible de détecter les données d'un autre fichier HTML dont le stockage est isolé → conserver le même nom/dossier de fichier ou passer par Export JSON / Importer ; `indexedDB.databases()` absent de certains navigateurs (la détection localStorage fonctionne toujours).

**Piste v1.9.6 / 2.0 :** sauvegarde dans un dossier choisi par l'utilisateur (File System Access API, Chrome/Edge) pour passer d'une version à l'autre malgré l'isolation du stockage.

**Statut :** ✅ Terminée — à valider par test manuel

---

### v1.9.6 — Petites corrections ✅ TERMINÉE
**Base :** v1.9.5

**Demandes :**
- Retirer la checkbox / le paramètre d'effacement à la fermeture (inutile)
- Remplacer la fenêtre `confirm()` d'avertissement sur le nombre de résultats par une fenêtre HTML comme celle du démarrage
- Corriger la mise en page de la section v1.9.5 dans l'historique des versions
- Réglages : pouvoir choisir « Empiler par », avec un texte d'avertissement, « Mois » par défaut
- Choix de période : liste « Année X / Janvier X / … / Décembre X »

**Implémentations :**
- Paramètre de nettoyage à la fermeture : plus aucune trace dans le code ; la clé `waav_cleanup_on_close` éventuellement laissée par un essai de la v1.9.5 est supprimée silencieusement au démarrage
- Avertissement de recherche : fenêtre `#search-warn-modal` (même style que `#resume-modal`), `applySearch()` devenue asynchrone ; boutons « Afficher les N résultats » / « Annuler la recherche » (restaure la recherche précédente) / « Régler les seuils d'avertissement » (ouvre ⚙ Réglages) ; Échap = annuler ; titre 🟡 ou 🔴 selon le seuil atteint ; `_searchBusy` maintenu pendant la fenêtre pour que la perte de focus du champ ne relance pas la recherche
- Historique des versions : cause = `.cl-item` en `display:flex`, ce qui séparait les balises `<code>` en colonnes distinctes ; corrigé en `display:block` + puce positionnée en absolu (`.cl-item::before`), style `.cl-item code` ajouté
- ⚙ Réglages : section « 📚 Affichage — empiler par défaut » (Mois recommandé / Jour / Année / Aucun), cadre d'avertissement orange sur la lenteur, bouton « Valeur par défaut (Mois) », mémorisation dans `localStorage` (`waav_collapse_default`) ; le choix s'applique tout de suite et définit la valeur au démarrage ; le sélecteur du panneau gauche reste utilisable temporairement
- Période : liste plate « Année 2025 », « Janvier 2025 » … « Décembre 2025 », puis l'année précédente ; années récentes d'abord, mois de janvier à décembre, années en gras ; seuls les mois contenant des messages sont listés
- Aide, changelog (v1.9.5 renommée « détection des anciennes bases », v1.9.6 ajoutée) et crédits mis à jour

**Vérifications :** `new Function(js)` OK ; équilibre des `<div>` OK ; test de bout en bout dans Chromium (Playwright) avec un export de 180 messages sur 2 années : liste de période, réglage « Empiler par », fenêtre d'avertissement (annuler / afficher / Échap), bouton ✕, historique des versions.

**Statut :** ✅ Terminée — à valider par test manuel

---

### v1.9.7 — Détection des anciennes bases sous Firefox ✅ TERMINÉE
**Base :** v1.9.6

**Constat (cas concret) :** archives laissées par une page v1.9.5 fermée sans effacer ; en ouvrant v1.9.6 sous Firefox 115 ESR (64 bits), rien n'est détecté, ni au démarrage ni via ⚙ Réglages.

**Analyse :**
- Cause principale (non contournable par le code) : Firefox isole le stockage par fichier HTML (clé = chemin complet du fichier). Une v1.9.6 enregistrée sous un autre nom/dossier n'a pas accès aux archives de la v1.9.5. Test Chromium : stockage IndexedDB partagé entre deux fichiers locaux (donc comportement différent selon le navigateur) ; Firefox non testable dans l'environnement de développement — hypothèse cohérente avec l'observation
- Cause secondaire (corrigeable) : `indexedDB.databases()` n'existe pas avant Firefox 126, donc aucune base IndexedDB ne pouvait être listée même dans un stockage commun

**Implémentations :**
- Repli sans `indexedDB.databases()` : sondage des noms connus (`wa_archive_v2`, `wa_v5`, `wa_v6`) via `idbExists()` (la base éventuellement créée par le sondage est annulée, aucune base fantôme) ; message d'information dans ⚙ Réglages
- ⚙ Réglages › « Stockage lié à » : affiche ce que contient réellement le stockage de ce fichier (nombre d'archives, version et date de dernière sauvegarde)
- Explication de l'isolation Firefox et de la solution durable (adresse fixe : hébergement web ou petit serveur local → même origine pour toutes les versions)
- Changelog et titre en v1.9.7

**Vérifications :** `new Function(js)` OK ; `<div>` équilibrés ; simulation de Firefox < 126 dans Chromium (suppression de `indexedDB.databases`) : base `wa_v5` détectée, `wa_archive` jamais listée ni supprimée, aucune base fantôme, suppression confirmée OK.

**Récupérer les données d'une autre version (procédure) :** rouvrir l'ancien fichier HTML (même chemin qu'avant) → Export → JSON → ouvrir la nouvelle version → Importer ce JSON.

**Statut :** ✅ Terminée — à valider par test manuel

---

### v1.9.8-test — Version de test (nom de fichier fixe) ✅ VALIDÉE
**Base :** v1.9.7

**Objectif :** vérifier, sous Firefox, qu'une nouvelle version enregistrée **sous le même nom et dans le même dossier** retrouve les données laissées par la précédente (détection + reprise à l'ouverture).

**Contenu :** strictement identique à la v1.9.7, hors numéro de version (`APP_VERSION='1.9.8-test'`, titre, badge, changelog). Livrée sous le nom fixe `waav.html`.

**Protocole de test :**
1. Dossier de test, par exemple `WAAV-test/` ; y copier la v1.9.7 renommée `waav.html`
2. Ouvrir `waav.html`, importer un petit `.txt`, fermer l'onglet **sans effacer**
3. Remplacer `waav.html` par la v1.9.8-test (même nom, même dossier), rouvrir
4. Attendu : fenêtre de reprise « enregistrées avec WAAV v1.9.7 … (vous êtes en v1.9.8-test) » ; « Reprendre la session » recharge les messages
5. Si rien n'apparaît : ⚙ Réglages › « Stockage lié à » (adresse du fichier, contenu du stockage), et vérifier que l'adresse est identique aux deux étapes

**Vérification automatique :** simulation dans Chromium avec profil persistant (A importe 180 messages, fichier écrasé par B, relance) : B détecte les données de A et les recharge.

**Résultat :** ✅ test concluant sous Firefox 115 ESR — avec le même nom et le même dossier, la nouvelle version retrouve les données de la précédente. **Règle retenue : le fichier s'appelle toujours `waav.html`, dans le même dossier ; les anciennes versions sont rangées ailleurs.**

---

### v1.9.8 — Confirmations HTML & nom de fichier fixe ✅ TERMINÉE
**Base :** v1.9.8-test (= v1.9.7)

**Demandes :**
- Passer en version stable avec le nom de fichier fixe `waav.html`, et avertir si le fichier porte un autre nom
- Fenêtres de confirmation de suppression au format HTML (bouton « Effacer », réglages), comme l'avertissement de recherche

**Implémentations :**
- `askConfirm({title,html,okLabel})` : fenêtre HTML générique `#confirm-modal` (même style que la fenêtre de démarrage, z-index au-dessus de toutes les autres fenêtres) → `Promise<boolean>` ; « Annuler » est le bouton par défaut (focus, Entrée, Échap) pour éviter une suppression par erreur ; bouton de suppression rouge
- Remplacement des 4 `confirm()` natifs : bouton « Effacer » (`clearData()`, avec nombre d'archives et de messages), « Supprimer les doublons » (`confirmDedup()`, avec le nombre), « Effacer et repartir de zéro » de la fenêtre de démarrage (`resumeClear()`), « Supprimer les anciennes bases » (`deleteLegacyData()`, liste des bases) — depuis ⚙ Réglages ou la fenêtre de démarrage. Plus aucun `confirm()` / `alert()` natif dans le code
- ⚙ Réglages › « Stockage lié à » : `updateFileNameWarning()` affiche un avertissement orange si le fichier (ouvert en `file://`) ne s'appelle pas `waav.html`, en précisant que renommer fait perdre la visibilité des données déjà enregistrées (export JSON conseillé avant)
- Version 1.9.8 (titre, badge, `APP_VERSION`, crédits, changelog)

**Vérifications :** `new Function(js)` OK ; `<div>` équilibrés ; test de bout en bout dans Chromium : doublons (annuler / confirmer), « Effacer » (Échap / confirmer), « Effacer et repartir de zéro » depuis la fenêtre de démarrage (la confirmation s'affiche au-dessus, annuler garde la fenêtre de démarrage), suppression d'une ancienne base depuis ⚙ Réglages (annuler / confirmer), avertissement de nom de fichier (absent pour `waav.html`, présent pour `autre.html`) ; aucun dialogue natif ouvert, aucune erreur JavaScript.

**Note :** la suppression d'un seul fichier dans la liste des archives (✕) n'a pas de confirmation (comportement inchangé).

**Statut :** ✅ Terminée — à valider par test manuel

---

### v2.0.0 — Navigation par jour depuis la recherche ✅ TERMINÉE
**Base :** v1.9.8

**Demandes :**
- Reprendre « Voir tout ce jour » et la navigation jour précédent / suivant, abandonnés en v1.9 → v1.9.2

**Diagnostic des échecs précédents :**
- En mode recherche, `computeFiltered()` ne garde que les messages trouvés : chaque groupe de jours (`dg._msgs`) est incomplet, le reste du jour n'existe pas dans la page
- Toute tentative passait par un `renderMessages()` complet, dont le défilement automatique différé (80 ms) écrase le positionnement demandé
- Aucun index des jours : pas de base pour « jour précédent / suivant »

**Implémentations :**
- Index des jours `dayIndex` (clé `AAAA-MM-JJ` → indices dans `allMessages`) et liste triée `dayKeys`, construits dans `buildPeriodIndex()` (une seule passe, `allMessages` étant déjà trié)
- Visualiseur de jour (`#day-viewer`) : panneau superposé, sans reconstruction de la liste de résultats ; affiche tous les messages du jour via `buildBubble()` (participants masqués exclus), résultats surlignés, défilement sur le premier résultat
- `openDayViewer(clé)`, `dayNav(±1)` (saute les jours sans message visible), `closeDayViewer()` ; boutons ‹ › désactivés aux extrémités ; flèches ← → et Échap au clavier (écoute en capture, inactive si une fenêtre de confirmation est ouverte)
- Bouton « 📅 Voir tout ce jour » sur le séparateur de chaque jour, uniquement en mode recherche
- Version 2.0.0 (titre, badge, `APP_VERSION`, en-tête, aide, changelog)

**Vérifications :** `new Function(js)` OK ; `<div>` équilibrés ; test de bout en bout dans Chromium sur une conversation fictive : boutons présents seulement avec une recherche, ouverture, comptage (messages / résultats), navigation ← → et ‹ › avec saut des jours absents, blocage aux extrémités, Échap, recherche et liste conservées ; aucune erreur JavaScript, aucun dialogue natif.

**Statut :** ✅ Terminée — validée par test manuel (16 novembre 2026)

**Réutilisation prévue :** `openDayViewer(clé)` servira au mini-calendrier (v2.2).

---

### v2.0.1 — « Voir toute la conversation » & « Aller à ce jour » ✅ TERMINÉE
**Base :** v2.0.0 (validée)

**Demandes :**
- Renommer le bouton des résultats de recherche « Voir tout ce jour » en « Voir toute la conversation »
- Ajouter dans le visualiseur de jour un bouton « Aller à ce jour », à côté de « Retour aux résultats », qui amène dans la conversation générale (hors recherche) au jour trouvé

**Implémentations :**
- Libellé du bouton : « 📅 Voir toute la conversation » (changelog de la v2.0.0 mis à jour en conséquence)
- `goToDay()` : ferme le visualiseur, termine la recherche (champ vidé, `searchQuery=''`), élargit la période à « Tous les messages » si elle ne contient pas le jour (cas possible après navigation ‹ › hors période), puis relance `computeFiltered()` et `renderMessages(false)` en renseignant `_pendingDayKey`
- `renderMessages()` : si `_pendingDayKey` est défini, déplie tous les groupes parents du jour (Année → Mois → Jour), charge ses bulles, puis, dans le `setTimeout` de fin de rendu, remplace le défilement habituel par `scrollIntoView` sur le jour et applique brièvement la classe `day-flash` (étiquette du jour mise en évidence 2,4 s) — le positionnement passe par le même rendu que le défilement final, d'où aucun conflit de timing
- Style du bouton « Aller à ce jour » (vert) distinct de « Retour aux résultats »
- Version 2.0.1 (titre, badge, `APP_VERSION`, en-tête, aide, changelog)

**Vérifications :** `new Function(js)` OK ; `<div>` équilibrés ; test de bout en bout dans Chromium (conversation fictive sur 3 mois) : libellé du bouton ; « Aller à ce jour » avec période « Tous » ; avec période « Mars » après navigation vers février (période élargie à « Tous ») ; recherche vidée, aucun surlignage résiduel, aucun bouton de recherche, jour chargé, groupes parents dépliés, jour visible à l'écran, mise en évidence puis retrait ; aucune erreur JavaScript.

**Statut :** ✅ Terminée — validée par test manuel (16 novembre 2026)

---

### v2.1.0 — Autonomie & confort ✅ TERMINÉE
**Base :** v2.0.1 (validée)

**Demandes :** (feuille de route 2.x, priorité 2)
- Héberger les polices et sql.js avec l'application (plus aucun chargement externe)
- Thème clair
- Confirmation avant suppression d'un fichier (✕)

**Choix techniques :**
- « Hébergé » = **intégré au fichier**, pour conserver le principe d'un seul fichier HTML (le fichier passe d'environ 138 Ko à environ 650 Ko)
- Polices : Plus Jakarta Sans en police variable (une seule ressource pour les graisses 300 à 700) et DM Mono 400/500, sous-ensembles latin (Fontsource, woff2) encodés en base64 dans des `@font-face`. Les caractères hors du sous-ensemble latin (par exemple polonais, turc) retombent sur la police sans-serif du système
- sql.js 1.10.2 (la même version qu'avant) : `sql-wasm.js` et `sql-wasm.wasm` compressés gzip puis base64 dans deux blocs `<script type="text/plain">` inertes, décompressés **à la demande** (`inflateAsset()` avec `DecompressionStream`, puis `loadSqlJs()` qui injecte le script par URL `blob:` et initialise avec `wasmBinary`). Aucun coût au démarrage hors lecture du fichier. Contrainte : Firefox 113+ / Chrome 80+ (Firefox 115 ESR compris)
- Licences : en-tête du fichier et `THIRD-PARTY-NOTICES.md` (SIL OFL 1.1 pour les polices, MIT pour sql.js) ; le code de WAAV reste en GPL-3.0-or-later

**Implémentations :**
- Thème : variables CSS surchargées par `:root[data-theme="light"]` (+ `color-scheme`), réglage dans ⚙ Réglages › Apparence (Sombre par défaut / Clair / Selon le système), clé `waav_theme` du `localStorage`, application anticipée par un petit script dans `<head>` (pas de flash au chargement), suivi du changement de thème système. Couleurs des expéditeurs assombries en thème clair, surlignage de recherche adapté
- ✕ d'un fichier : `confirmRemoveFile()` → `askConfirm()` (nombre de messages indiqué) puis `removeFile()` ; pour le dernier fichier, on garde la confirmation propre à « Effacer » (`clearData()`), pour ne pas demander deux fois

**Vérifications :** `new Function(js)` OK ; `<div>` équilibrés ; test Chromium **avec tout accès réseau bloqué** : 0 requête externe, polices chargées depuis le fichier, export SQLite produit un `.db` valide (relu avec sqlite3 : 2 lignes), thème clair appliqué, conservé après rechargement, confirmation ✕ (annuler laisse 2 fichiers, confirmer en laisse 1) ; aucune erreur JavaScript. Non testé : Firefox (export SQLite à vérifier en réel)

**Statut :** ✅ Terminée — à valider par test manuel (notamment l'export SQLite sous Firefox et l'aspect du thème clair)

---

### v2.1.1 — Choix de la police & easter egg partout ✅ TERMINÉE
**Base :** v2.1.0

**Demandes :**
- Laisser l'utilisateur choisir la police ; garder la police précédente
- Les emoji paraissaient moins jolis depuis la v2.1.0
- Voir l'easter egg R&R (oie 🪿) ailleurs que dans Chrome

**Diagnostic :**
- Une police de texte ne contient pas d'emoji : ceux-ci viennent de la police emoji du système. 🪿 (Unicode 15, 2022) manque à de nombreux systèmes, d'où une oie visible seulement dans certains navigateurs
- Cause probable de l'aspect des emoji (non reproductible sans la machine de l'utilisateur) : les `@font-face` intégrés de la v2.1.0 n'avaient pas de `unicode-range`, contrairement au CSS de Google Fonts qui limitait chaque fichier au jeu « latin ». Un navigateur peut alors traiter la police comme couvrant tous les caractères et choisir un autre repli pour emoji et symboles

**Implémentations :**
- `unicode-range` du jeu « latin » restauré sur les trois `@font-face` (mêmes plages que Google Fonts) : les emoji et symboles retournent directement aux polices du système
- Variable CSS `--font-main` et attribut `data-font` : ⚙ Réglages › Apparence › Police = « Plus Jakarta Sans » (par défaut) ou « Police du système » (`system-ui`, Segoe UI, Roboto…) ; clé `waav_font` du `localStorage`, appliquée dès l'amorçage dans `<head>`
- Easter egg : l'oie et la tortue sont des images SVG intégrées (dessins Noto Color Emoji extraits en SVG autonomes, environ 8 Ko chacune en base64) au lieu d'emoji texte ; le message de fin ne contient plus d'emoji. Licence OFL ajoutée à `THIRD-PARTY-NOTICES.md`
- Choix écarté : une police emoji embarquée (Noto Color Emoji complet : plusieurs Mo, format SVG non rendu par Chrome)

**Vérifications :** `new Function(js)` OK ; `<div>` équilibrés ; Chromium : police par défaut, choix « système », persistance après rechargement, liste synchronisée ; easter egg : images chargées, oie et tortue visibles en mouvement, retrait et message final ; 0 requête externe, aucune erreur JavaScript

**Statut :** ✅ Terminée — à valider par l'utilisateur (aspect des emoji dans son navigateur, easter egg sous Firefox)

---

## Fonctionnalités abandonnées

| Fonctionnalité | Version tentée | Raison |
|----------------|----------------|--------|
| "Voir tout ce jour" (recherche) | v1.9, v1.9.1, v1.9.2 | Non fonctionnel malgré 3 tentatives — repris et livré en v2.0.0 (visualiseur de jour) |
| Navigation jour précédent/suivant | v1.9, v1.9.1, v1.9.2 | Non fonctionnel malgré 3 tentatives — repris et livré en v2.0.0 |

---

## Roadmap — Version 2.x

| Version | Contenu | État |
|---------|---------|------|
| **2.0.0** | Visualiseur de jour depuis la recherche, navigation jour précédent / suivant | ✅ Validée |
| **2.0.1** | Bouton renommé « Voir toute la conversation », bouton « Aller à ce jour » | ✅ Validée |
| **2.1.0** | Autonomie et confort : polices et sql.js intégrés au fichier, thème clair, confirmation de suppression d'un fichier (✕) | ✅ Validée |
| **2.1.1** | Choix de la police, retour aux emoji du système, easter egg visible dans tous les navigateurs | ✅ Livrée, à valider |
| **2.2** | API d'IA indépendante du fournisseur (adresse, clé et modèle réglables ; Claude, OpenAI ou modèle local) : résumés, questions-réponses, analyse du ton ; activation explicite, seuls les messages sélectionnés sont envoyés | À faire |
| Ensuite | Mini-calendrier (via `openDayViewer`), marque-pages, affichage des médias, synchronisation MySQL (API PHP) | À planifier |

---

## Bonnes pratiques établies

1. **Toujours partir de la dernière version stable validée** (actuellement v1.9.8)
2. **Un seul changement fonctionnel à la fois** avec vérification JS entre chaque
3. **Vérification systématique** : `new Function(js)` + liste de checks ciblés
4. **Incrémenter la version** à chaque modification
5. **Mettre à jour le changelog in-app** ET ce document MD à chaque version
6. **Conserver les versions précédentes** (ne jamais écraser)
7. **Tester le rechargement depuis IndexedDB** après chaque modification de `loadFromStorage`
8. **Ne jamais patcher un fichier déjà patché plusieurs fois** — repartir de la base stable
9. **En usage local, toujours nommer le fichier `waav.html`** et le garder dans le même dossier (voir « Stockage » dans le README) ; ranger les anciennes versions ailleurs

---

## Contexte technique — export MySQL

| Paramètre | Valeur |
|-----------|--------|
| Base de données cible | MySQL / MariaDB (compatible phpMyAdmin) |
| Jeu de caractères | `utf8mb4` |
| Interclassement | `utf8mb4_unicode_ci` |
| Format d'import | Fichier `.sql` généré par l'export MySQL de WAAV |

---

*Document mis à jour le 16 novembre 2026 — WAAV v2.1.1*
