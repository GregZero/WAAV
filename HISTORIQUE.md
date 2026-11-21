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
| Version stable actuelle | v2.3.0 en cours de validation (dernière version validée : v2.2.1 ; v2.2.2 et v2.2.3 en cours d'essai par l'utilisateur : « fonctionne très bien sur PC ») |

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

### v2.1.2 — Correctifs téléphone & résultats précédent / suivant ✅ TERMINÉE
**Base :** v2.1.1

**Bugs signalés (Chrome téléphone) :**
- Après « Aller à ce jour », l'entête et le bouton du menu de gauche disparaissent : écran bloqué
- Visualiseur de jour : entête illisible (titre écrasé, boutons qui se chevauchent), navigation précédente / suivante peu pratique

**Diagnostic :**
- Cause 1 : `scrollIntoView({block:'start'})` ne fait pas défiler que la liste des messages : il fait aussi glisser les conteneurs parents, document compris, même en `overflow:hidden`. Sur téléphone, `100vh` correspond à l'écran sans barre d'adresse : la page est plus haute que la zone visible et le navigateur la décale d'autant (66 px = la hauteur de l'entête). Reproduit en émulation mobile en forçant la page à 850 px : entête à −66 px, `scrollTop` du document à 66
- Cause 2 : l'entête du visualiseur n'avait aucune règle pour les petits écrans

**Implémentations :**
- `scrollWithin(conteneur, élément, centré)` remplace tous les `scrollIntoView` (visualiseur, « Aller à ce jour », résultat de recherche) ; `resetPageScroll()` remet le document, `<body>` et `#main` à zéro (appelé à l'ouverture et à la fermeture du visualiseur et après « Aller à ce jour »), avec des écouteurs `scroll` sur la fenêtre et sur `#main` comme garde-fou
- `body` : `height:100vh;height:100dvh` (hauteur dynamique, repli sur `vh`)
- Visualiseur sur petits écrans (≤ 600 px) : panneau plein écran, entête sur deux lignes (‹ titre › puis « Aller à ce jour » / « Retour aux résultats »)
- **Occurrences** (choix C) : barre « ▲ Précédent · Résultat n / N · Suivant ▼ » sous l'entête, visible quand une recherche est active. Parcourt les résultats un à un (messages de `filteredMsgs`), à l'intérieur du jour puis de jour en jour ; boutons désactivés aux extrémités ; résultat courant entouré (`.dv-current`) ; sur un jour sans résultat, la barre affiche le total et permet de rejoindre le résultat précédent / suivant. Les boutons ‹ › gardent leur rôle : jour précédent / suivant de l'archive
- Index des résultats (`buildHitIndex`) recalculé à chaque ouverture ou changement de jour : une passe sur les résultats

**Vérifications :** `new Function(js)` OK ; `<div>` équilibrés ; plus aucun `scrollIntoView` ; Chromium émulation mobile (390 px, page forcée à 850 px) : entête à 0 et document non décalé après « Aller à ce jour » ; parcours complet des 7 résultats vers l'avant (compteur 3/7 → 7/7, « Suivant » désactivé à la fin) et retour au début (« Précédent » désactivé) ; ‹ › jour par jour ; capture du panneau lisible ; tests de la v2.0.1 (Aller à ce jour, période élargie) rejoués sans régression ; aucune erreur JavaScript

**Statut :** ✅ Terminée — à valider sur le téléphone

---

### v2.3.0 — IA : recherche ciblée sur la sélection 🟡 À VALIDER

- **Base** : v2.2.7 (pause et reprise validées par l'utilisateur).
- **Demandes** : questions « sur toute l'archive » sans envoyer 100 000+ messages ; mots-clés proposés par l'IA (qui ne reçoit que la question) et validables ; si trop de résultats, demander à chaque fois (garder les meilleurs / tout traiter / affiner) ; contexte ±2 messages ; recherche appliquée à la sélection actuelle (période et participants) ; boutons Suspendre / Annuler et messages d'état dans la carte de la demande, ligne sous la zone de saisie supprimée.
- **Implémentation** : choix de portée « Sélection actuelle | Recherche ciblée » (mémorisé) sous la zone de saisie ; flux en cartes (`_aiTarget`) : (1) mots-clés (`aiTargetKeywords`, une seule requête avec « Question : … », réponse JSON analysée par `aiParseKw`, pastilles modifiables, ajout, retrait, « Redemander à l'IA », « Mots-clés à la main » sans appel) ; (2) recherche exacte par WAAV (`aiTargetSearch`) : texte normalisé (accents, casse) mis en cache sur les messages (`m._n`), mot = début de mot (« vacance » trouve « vacances »), guillemets = expression exacte, masque de bits par mot-clé (30 max), doublons exclus ; passages = résultats ±2 messages fusionnés, comptage par mot-clé, participant et mois (histogramme) ; (3) si tout tient en une requête (taille de partie − 600 caractères) : confirmation d'envoi directe ; sinon carte de décision : « Garder les N meilleurs » (classement : nombre de mots-clés distincts puis occurrences, sélection tournante par mois pour répartir dans le temps), « Tout traiter (≈ parties, requêtes, durée) » (ancienne mécanique par parties + fusions) ou « Affiner » ; (4) envoi des passages (`— Passage du JJ/MM/AAAA —`) avec les faits calculés par WAAV dans la consigne ; réponse avec dates cliquables (`aiLinkDates` : seules les dates existant dans l'archive deviennent des liens, ouverture du jour) ; l'IA est avertie de ne jamais conclure à l'absence d'un sujet ; réglage « Je valide / Automatique » (`kwAuto`) ; `tgt` (mots-clés, trouvés, envoyés) conservé dans la demande, la pause, l'export d'état et l'historique ; `aiPlanP` factorise le plan d'envoi ; carte de la demande en cours : boutons « ⏸ Suspendre » et « Annuler » ; carte en pause : « Reprendre », « Exporter l'état », « Supprimer » ; `#ai-status` et les boutons du pied de page supprimés.
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright avec faux serveur (3 000 messages fictifs, comptes comparés à un calcul Python indépendant) : comptes exacts (14 messages / 14 passages pour « notaire » ; participants 9 + 5) ; requête de mots-clés = question seule ; requête de réponse = passages seulement (4 017 caractères contre 143 964 pour la conversation) avec faits ; clic sur une date ouvre le bon jour ; cas sans résultat ; mots-clés à la main (1 seule requête) ; cas « trop de résultats » : carte de décision à 3 choix, « garder les meilleurs » = 1 requête, « tout traiter » = 28 requêtes ; mode automatique ; suspension depuis la carte, rechargement, reprise, `tgt` dans l'historique ; mobile 390 px ; non-régression : réponses longues défilantes, pause / reprise / export / import d'état (mêmes chiffres qu'en 2.2.7).
- **Non testé** : qualité des mots-clés proposés par de vrais modèles, durée réelle sur 471 685 messages (la recherche est exacte et sans IA), Firefox, téléphone réel.
- **À savoir** : avec des mots-clés très fréquents (budget, vacance…), les passages se fusionnent et couvrent presque toute la conversation : la carte de décision apparaît presque toujours ; mieux vaut des mots-clés rares ou une période réduite. La recherche est un préfixe de mot : « budget » trouve aussi « budgétaire » (la normalisation retire l'accent) ; un mot court trop général donnera beaucoup de résultats. La recherche par le sens (embeddings) est prévue en 2.3.1.

### v2.2.7 — IA : demandes suspendables et reprenables 🟡 À VALIDER

- **Base** : v2.2.6.
- **Demande** : pouvoir suspendre une demande IA, l'enregistrer (ou l'exporter) et la reprendre plus tard (manque de quota, extinction du PC), pour de très grosses périodes (cas réel signalé : 471 685 messages, 2 066 parties, modèle local).
- **Principe** : « journal de rejeu ». Chaque réponse de l'IA est enregistrée dans l'ordre (`steps`), avec les parties découpées (`parts`) et les métadonnées (`kv`), dans une base IndexedDB **séparée** `waav_ia` (`wa_archive` n'est jamais touchée). À la reprise, le traitement est rejoué depuis le début : les étapes déjà enregistrées sont servies instantanément sans requête, puis les appels réels reprennent. Comme le découpage des fusions est déterministe, la taille de fusion (`chunk`) est enregistrée avec la demande.
- **Interface** : bouton « ⏸ Suspendre » (termine l'étape en cours, y compris pendant une attente de limite de débit) ; carte « Demande en pause » (avancement, jetons consommés, date, modèle ; ↻ Reprendre, Exporter l'état, Supprimer la demande) affichée aussi après rechargement ; « Importer un état » (zip ou json) ; confirmation avant d'écraser une demande en pause ; confirmation si le modèle change à la reprise ; toute erreur (429 persistant, quota, crédits) laisse la demande en pause avec le travail enregistré ; avertissement à la fermeture de l'onglet pendant une demande ; `navigator.wakeLock` quand disponible. Une seule demande en pause à la fois.
- **Export d'état** : `waavAiJob` (parties + résultats obtenus) dans un zip ; l'import accepte aussi l'instantané brut de la 2.2.6 (ligne de console `JSON.stringify({waavAiJob:1,job:_aiJob})`), converti en journal (parties de phase 1 + éventuelles fusions du premier niveau).
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright (bureau + mobile) avec faux serveur : référence 22 requêtes ; suspension après 6 étapes puis rechargement puis reprise = 16 requêtes (22 − 6) ; erreur 429 persistante → pause → rechargement → reprise ; export zip contrôlé par `unzip -t`, suppression, import, reprise = 15 requêtes (22 − 7) ; instantané 2.2.6 importé → reprise = 14 requêtes (22 − 8) ; confirmations de remplacement et de changement de modèle ; base vidée après succès. Un bug du rendu de la carte (progression affichée après une suspension) a été corrigé en cours de test.
- **Non testé** : très gros volumes réels (≈ 40 Mo de parties dans IndexedDB), Firefox, écran de veille réel, extinction réelle du PC.
- **À savoir** : l'avancement affiché (« 6 / 27 étapes ») repose sur une estimation du nombre de fusions, souvent un peu haute ; la barre se rattrape à la fin. WAAV ne travaille que page ouverte.

### v2.2.6 — Zip : import, export et nom du fichier d'export 🟡 À VALIDER

- **Base** : v2.2.5.
- **Demandes** : importer le fichier texte WhatsApp depuis une archive zip ; exporter aussi en zip ; choisir le nom de l'export (les exports IA `.md` ne sont pas concernés pour le moment).
- **Implémentation import** : `.zip` accepté par le sélecteur et le glisser-déposer ; lecteur zip intégré sans bibliothèque (`zipEntries`, `zipRead`, `expandZips`) : lecture de la fin d'archive (EOCD) et du répertoire central par `slice`, décompression du seul fichier voulu par `DecompressionStream('deflate-raw')` (ou stockage direct) ; seuls les `.txt` et `.json` sont extraits (médias, `__MACOSX`, `._*` ignorés) ; nom dans la liste : « archive.zip › fichier.txt » (évite les collisions entre plusieurs `_chat.txt` d'iPhone) ; messages d'erreur pour zip invalide, protégé par mot de passe, zip64 (> 4 Go), méthode non prise en charge, navigateur sans décompression.
- **Implémentation export** : `downloadBlob` centralise nom et zip : champ « Nom du fichier » (pré-rempli `waav_<période>_<AAAAMMJJ-HHMM>`, modifiable ; caractères interdits `\ / : * ? " < > |` remplacés ; extension ajoutée automatiquement sans doublon ; champ vide = ancien nom par défaut) et case « Compresser en .zip » (mémorisée, `waav_exportzip`) valable pour TXT, JSON, MySQL et SQLite ; écriture du zip maison (`zipBlob`, CRC-32, noms UTF-8, deflate par `CompressionStream('deflate-raw')`, stockage non compressé si indisponible) ; menu d'export élargi à 240 px.
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright avec des zips fabriqués avec Python (deflate avec médias de 3 Mo + `__MACOSX` + dossier, stocké, faux zip, zip sans texte) : import correct (1 500 messages) et messages d'erreur clairs ; exports TXT / JSON / MySQL / SQLite en zip contrôlés par `unzip -t` (aucune erreur), contenu TXT identique (1 500 lignes, accents conservés, nom interne = nom choisi), zip de 12 Ko pour 131 Ko de texte ; noms : personnalisé, extension déjà saisie, caractères interdits (`a/b:c*?` → `a_b_c__`), champ vide ; aller-retour TXT et JSON zip → 1 500 messages.
- **Non testé** : vrai export WhatsApp (iPhone / Android) avec médias volumineux, zips très gros (> 1 Go), zip protégé (non créé en test), Firefox, mémoire sur de très gros exports.
- **À savoir** : l'emplacement d'enregistrement reste décidé par le navigateur (pas de fenêtre « Enregistrer sous » commandée par WAAV dans Firefox).

### v2.2.5 — Correctif : réponses IA longues 🟡 À VALIDER

- **Base** : v2.2.4.
- **Problème signalé par l'utilisateur** (réponse d'un résumé sur 39 625 messages) : la carte de réponse était coupée et la zone des échanges ne défilait pas.
- **Diagnostic** : `#ai-thread` est une colonne flex avec défilement ; les cartes `.ai-card` (`overflow:hidden`) avaient `flex-shrink:1` et se réduisaient à la hauteur disponible au lieu de laisser le conteneur défiler. Reproduit en test (carte de 362 px pour un contenu de 3 442 px) avec une réponse de 80 lignes.
- **Correctif** : `flex-shrink:0` sur les cartes et sur tous les enfants directs de `#ai-thread`.
- **Vérifications** : mêmes tests Playwright (bureau 1280×640 et mobile 390 px) : carte à sa hauteur complète, zone défilable à la molette jusqu'au bas ; pas d'erreur de page.
- **Non testé** : Firefox, téléphone réel.
- **Leçon** : mes tests précédents n'utilisaient que de courtes réponses ; un test avec une longue réponse est désormais systématique pour tout élément à défilement.

### v2.2.4 — IA : progression, réponses pliables, bloc condensé 🟡 À VALIDER

- **Base** : v2.2.3 (essais utilisateur positifs sur PC).
- **Demandes** : (1) avancée des requêtes (partie X/X) et temps de livraison estimé dans la bulle « Réponse en cours… » ; (2) réponses pliables / dépliables au niveau du titre, icônes discrètes poubelle et disquette, bas de réponse conservant date, modèle, etc. ; (3) bloc « IA — informations » condensé avec engrenage vers les réglages, notions de requêtes et jetons limitées au strict nécessaire.
- **Implémentation** : bulle de progression (`aiPendUpdate`, rafraîchie chaque seconde) : étape (« Partie 4 / 18 », fusion, synthèse), barre, temps écoulé, temps restant estimé = moyenne des étapes faites × étapes restantes ; avant la 1re étape : estimation de la limite de débit ou de la latence apprise (`waav_ai_lat`, moyenne glissante par fournisseur et modèle) ; cartes `aiCard` (titre = consigne, date courte, chevron, icônes disquette / poubelle en SVG, « Tout plier / Tout déplier », état de pliage en mémoire) ; bloc de gauche : engrenage, « N messages · envoi en P parties · ≈ durée », quota en une ligne (« Ce mois-ci » + barre, « 472 k / 1 Md jetons (+ ≈ 37 k) »), « Local · sans quota » pour Ollama, avertissement seulement si la fenêtre de contexte est trop petite (la jauge de contexte, toujours < 50 % puisque WAAV découpe, a été retirée) ; titre de la zone de droite : « Sélection : N messages » ; confirmation d'envoi : parties et durée estimée ; sur mobile, changer de mode referme le tiroir de gauche.
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright (bureau + mobile 390 px, requêtes ralenties à 350 ms) : bulle avec « Partie 4 / 18 », « écoulé / ≈ restantes » et barre, disparition à la fin, cartes, pliage d'une carte puis de toutes (aria-expanded, bouton qui change de libellé), export par disquette (sans clé), suppression par poubelle (stockage mis à jour), historique retrouvé après rechargement, latence mémorisée, erreur 429 affichée dans la carte puis reprise.
- **Non testé** : appels réels (Ollama, Mistral, Claude), précision de l'estimation du temps sur de vrais serveurs, Firefox, téléphone réel.
- **À savoir** : l'estimation du nombre d'étapes de fusion est approximative (elle suppose des résumés de partie d'environ 1 500 caractères) ; la barre peut donc sauter en fin de traitement.

### v2.2.3 — Mode IA : Conversations / IA, historique conservé 🟡 À VALIDER

- **Base** : v2.2.2 (essais en cours, « semble bien fonctionner »).
- **Demandes** : remplacer la fenêtre modale IA par un mode d'affichage (bouton « Conversations / IA ») ; volet gauche inchangé avec un bloc IA en dessous (infos, jauges, réglages) ; zone de recherche sous période et participants ; historique des échanges conservé après rechargement, commun à toutes les archives ; maquette validée avant de coder (artefact Design).
- **Implémentation** : `setViewMode('conv'|'ai')` (classe `ai-mode` sur `#main` ; `#messages-area` et `#empty-state` masqués par CSS, position de défilement de la conversation sauvegardée et restaurée) ; `#ai-view` (en-tête, fil `#ai-thread`, suggestions dont « Résumer la période » en premier, saisie, Envoyer / Annuler / Reprendre) ; bloc `#ai-side` dans le volet gauche (infos, jauges, fournisseur, lien vers les réglages) rafraîchi à chaque changement de sélection (`computeFiltered` enveloppée, délai 120 ms) ; recherche déplacée dans le DOM sous les participants ; fenêtre modale supprimée ; historique `waav_ai_hist` (localStorage, 100 échanges max : date, fournisseur, modèle, sélection, fichiers d'origine, consigne, réponse, jetons — ni clé ni messages bruts), réglage « Historique des échanges » (conserver / ne pas conserver), suppression par échange et « Effacer l'historique » (confirmation), export par échange et « Exporter tout (.md) ». La base `wa_archive` n'est pas touchée.
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright (bureau + mobile 390 px) avec faux serveur : ordre des blocs du volet gauche, bouton IA grisé par défaut puis actif, bascule et retour (défilement restauré à 300 px), infos qui suivent la sélection (calendrier), 4 requêtes pour 1 500 messages, rendu Markdown sans injection, 2 échanges stockés sans clé, rechargement (échanges retrouvés), suppression, effacement, historique désactivé (clé de stockage supprimée), exports, erreur 429 puis reprise ; non-régression du calendrier, de la recherche, du mode classique et du test de connexion.
- **Non testé** : appels réels (Ollama, Mistral, Claude), Firefox, téléphone réel (tiroir de gauche et clavier virtuel), très gros historiques.
- **À savoir** : après un rechargement, la zone d'import recouvre l'écran tant qu'aucune archive n'est rechargée : l'historique est donc consultable une fois l'archive restaurée.

### v2.2.2 — Calendrier de période et aide à la connexion API 🟡 À VALIDER

- **Base** : v2.2.1 validée.
- **Demandes** : choix de période plus fin (calendrier dépliable à cases à cocher : années / mois / semaines / jours), valable pour tout WAAV ; message d'erreur CORS précis, bouton « Tester la connexion », liste et choix du modèle.
- **Décisions** : semaines placées sous chaque mois (une semaine à cheval sur deux mois apparaît dans les deux, avec ses jours du mois) ; arbre dépliable ; ancien menu conservé en repli (réglage « Période — filtre » : Calendrier / Classique) ; sélection non conservée entre sessions (démarrage sur « Tout »).
- **Implémentation calendrier** : état `selectedDays` (Set de clés `AAAA-MM-JJ`, `null` = tous) ; `computeFiltered()` s'appuie sur `calBase()` quand il est défini, sinon sur l'index de période existant ; arbre `calTree` / `calNodes` construit une fois (`buildCalendar()`), lignes enfants créées à l'ouverture ; cases à trois états (`indeterminate`) ; application différée de 200 ms ; `goToDay()` élargit la sélection si le jour est hors sélection ; en-tête, export MySQL et suffixe d'export tiennent compte de la sélection ; `buildDayIndex()` remet `selectedDays` à `null` (sélection réinitialisée quand les archives changent).
- **Implémentation connexion** : `aiNetErrMsg()` (cause probable et valeur exacte d'`OLLAMA_ORIGINS` : « * » pour un fichier local, sinon l'origine de la page) ; `aiListModels()` / `aiTest()` (GET `/models`, aucun message envoyé) ; liste déroulante des modèles avec « Autre… » ; blocs `<think>…</think>` retirés des réponses.
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright (bureau + mobile 390 px) avec faux serveur : décocher un mois (1 286 / 1 500 messages attendus et obtenus), case indéterminée, semaine à cheval présente dans les deux mois, recherche et transcript IA limités à la sélection, « Aucun » / « Tout », `goToDay` hors sélection, mode classique et retour, test de connexion (liste, choix, échec CORS avec message). Performance sur 100 000 messages fictifs : construction du calendrier 7 ms, application d'une sélection 6 ms.
- **Cas rencontré chez l'utilisateur** : Firefox + Ollama local : 403 sans en-tête CORS tant que `OLLAMA_ORIGINS` n'autorise pas l'origine (« null » pour un fichier local ouvert directement).
- **Non testé** : appels réels (Ollama, Mistral, Claude) et listes de modèles réelles, Firefox, affichage du panneau sur téléphone réel.

### v2.2.1 — Panneau IA : lisibilité, export, jauge de jetons ✅ VALIDÉE

- **Base** : v2.2.0 (section IA déplacée dans les Réglages).
- **Demandes** : résultat plus lisible ; export avec prompt et résultat ; « ✨ Résumer la période » en simple suggestion, en premier ; échelle / comparatif de consommation de jetons avec un plan gratuit.
- **Implémentation** : mini-rendu Markdown sûr (échappement avant mise en forme : titres, listes, gras, italique, code) ; consigne système demandant du Markdown simple ; un seul bouton « Envoyer » ; export `.md` (date, fournisseur, modèle, sélection, jetons, consigne système, prompt, résultat — jamais la clé ni les messages) ; jauge : plus grosse requête vs fenêtre de contexte, consommation du mois (compteur local `waav_ai_usage`, d'après les `usage` renvoyés par l'API) vs quota de référence réglable ; réglages « fenêtre de contexte » et « quota mensuel de référence » ; taille des parties dérivée de la fenêtre de contexte (au lieu de 24 000 caractères fixes) ; notes par fournisseur.
- **Repères trouvés sur le web (18/11/2026, modifiables)** : Mistral, palier gratuit « Experiment » : 1 requête/s, 500 000 jetons/min, 1 milliard de jetons/mois par modèle (aide Mistral) ; des sources tierces indiquent qu'il exige d'accepter l'utilisation des données pour l'entraînement (à vérifier chez Mistral). API Claude : pas de palier gratuit permanent, petits crédits à l'inscription (montant non publié). Ollama : local, sans quota.
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright avec faux serveur (bureau + mobile 390 px) : suggestion en premier, rendu sans injection de script, 4 requêtes pour 1 500 messages, compteur de jetons, export sans clé ni messages bruts.
- **Correctifs longues périodes (14 650 messages avec Mistral)** : (1) réglages migrés (`v:2`) : l'absence de « fenêtre de contexte » donnait celle d'Ollama (8 192), d'où 65 requêtes au lieu de ≈ 20 ; (2) erreur 429 : WAAV attend (en-tête `retry-after`, sinon 15/30/60/120 s) et réessaie jusqu'à 5 fois, avec compte à rebours ; (3) régulation préventive : « limite de jetons par minute » (Mistral 500 000) et « délai minimal entre requêtes » (1 s), durée estimée affichée dans la confirmation ; (4) fusion des résumés par étapes (groupes tenant dans la fenêtre de contexte), parties limitées à 200 mots, fusions intermédiaires à 250 mots ; (5) reprise : en cas d'échec ou d'annulation, parties et fusions déjà obtenues conservées, bouton « ↻ Reprendre » ; (6) explication de l'assistance IA et du découpage ajoutée dans l'aide.
- **Vérifications correctifs** : tests Playwright avec faux serveur : migration des réglages, 429 unique (attente puis succès), 429 persistant puis reprise, écarts ≥ 1 s entre requêtes, fusion multi-niveaux (24 requêtes, aucune requête au-dessus du plafond de partie), mobile 390 px.
- **Non testé** : appels réels (Ollama, Mistral, Claude), limite réelle du compte Mistral (console Mistral > Limits), Annuler en cours de requête, Firefox ; fenêtres de contexte par défaut (Ollama 8 192, Mistral 32 000, Claude 200 000) à ajuster selon le modèle.

### v2.2.0 — IA : résumé de période et questions (optionnel) 🟡 À VALIDER

- **Base** : v2.1.2 validée.
- **Demandes** : fournisseurs Ollama (local), Mistral AI et Claude ; résumer une période, puis questionner la sélection ; interrupteur général dans les réglages ; bouton « Résumer » sous la période et les participants ; clé au choix (session par défaut, ou mémorisée).
- **Décisions** : 2.2.0 = réglages + panneau + résumé/questions sur la **sélection en cours** ; 2.2.1 = questions sur toute l'archive (recherche d'abord, envoi d'une sélection seulement) ; recherche sémantique locale (embeddings) et statistiques : versions ultérieures.
- **Implémentation** : réglages `waav_ai` et `waav_ai_key` (localStorage, jamais exportés) ; deux formats d'appel (compatible OpenAI pour Ollama et Mistral, Anthropic avec en-tête d'accès direct navigateur) ; découpage en parties de ≈ 24 000 caractères, résumé par partie puis synthèse finale ; confirmation avant envoi (nombre de messages, jetons estimés, destination, nombre de requêtes) ; annulation (`AbortController`) ; erreurs lisibles (clé refusée, quota, modèle, connexion/CORS) ; réponse affichée en texte brut.
- **Vérifications** : syntaxe JS, `<div>` équilibrés, tests Playwright avec faux serveur d'IA (bureau + mobile 390 px) : bouton grisé par défaut, 6 requêtes pour 1 500 messages, question transmise, format Claude et en-têtes, erreur 401, désactivation.
- **Correction avant validation** : la section « 🤖 IA » avait été insérée par erreur dans la fenêtre d'aide au lieu des Réglages (repérée par l'utilisateur sur la version en ligne) ; déplacée dans les Réglages et vérifiée par test Playwright (bureau + mobile).
- **Non testé** : appels réels vers Ollama, Mistral et Claude (CORS à vérifier chez l'utilisateur), bouton Annuler en cours de requête, Firefox.

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
| **2.1.1** | Choix de la police, retour aux emoji du système, easter egg visible dans tous les navigateurs | ✅ Validée |
| **2.1.2** | Correctifs téléphone (« Aller à ce jour », visualiseur) et boutons résultat précédent / suivant | ✅ Livrée, à valider |
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

*Document mis à jour le 17 novembre 2026 — WAAV v2.1.2*
