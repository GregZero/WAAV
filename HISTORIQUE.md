# WAAV — Historique du projet (version condensée)

> Document de référence du développement : état, architecture, versions, décisions, roadmap. Retour au [README](README.md).
> Le détail version par version (demandes, diagnostics, vérifications) des versions 1.0 à 2.4.0 est archivé dans [`docs/HISTORIQUE-ARCHIVE.md`](docs/HISTORIQUE-ARCHIVE.md) : **ne le lire que pour une information précise** (chercher `### vX.Y.Z`).

---

## Informations générales

| Champ | Valeur |
|-------|--------|
| Application | WAAV — WhatsApp Archive Viewer : un seul fichier `index.html` autonome (HTML5, CSS3, JavaScript vanilla, IndexedDB), aucune donnée envoyée par défaut |
| Concept original | R&R (Régori & Rabrielle) — développement : Claude (Anthropic) |
| Licence | GPL-3.0-or-later (composants tiers : `THIRD-PARTY-NOTICES.md`) |
| Démarrage | Septembre 2026 |
| Dépôt / publication | https://github.com/GregZero/WAAV — GitHub Pages : https://gregzero.github.io/WAAV/ (l'utilisateur dépose lui-même les fichiers : Claude ne peut pas pousser sur GitHub depuis le chat) |
| Dernière version livrée | **v2.4.2** |
| Dernière version validée formellement | v2.2.1 (versions 2.2.2 à 2.3.0 utilisées et testées par l'utilisateur, « fonctionne très bien sur PC » ; 2.4.0 : en première utilisation, retours sur les mots-clés ; recherche par le sens **jamais testée avec le vrai modèle**) |
| Fichier du dépôt | `index.html` (≈ 840 Ko), `README.md`, `HISTORIQUE.md`, `THIRD-PARTY-NOTICES.md`, `LICENSE`, `.gitignore`, `docs/` |

---

## Architecture technique

**Stockage** : IndexedDB `wa_archive` (archives : stores `files` + `meta`) ; `waav_ia` (demande IA en pause : stores `kv` = parts + métadonnées, `steps` = réponses de l'IA dans l'ordre) ; `waav_sem` (index de la recherche par le sens : stores `meta`, `batches` de 128 passages, vecteurs entiers 8 bits). `localStorage` : `waav_theme`, `waav_font`, `waav_periodmode`, `waav_exportzip`, `waav_ai` (réglages IA), `waav_ai_key` (clé si « mémorisée »), `waav_ai_hist` (historique, 100 échanges max), `waav_ai_usage` (jetons du mois), `waav_ai_lat` (latence apprise), `waav_ai_scope`, `waav_ai_src`, `waav_sem` (réglages), drapeaux d'essai `waav_sem_fake` / `waav_sem_fake_delay` (modèle simulé, pour les tests).

**Rendu** : par groupes Année › Mois › Jour, bulles créées à l'ouverture du groupe (v1.8+). `computeFiltered()` → `filteredMsgs`, enveloppée pour rafraîchir le panneau IA.

**Parsing** : `JJ/MM/AAAA, HH:MM - Expéditeur: message` et variantes ; import JSON et zip (lecteur zip maison, sans bibliothèque).

**Principes** : un seul fichier HTML ; rien n'est envoyé sans action explicite ; les deux options (IA, recherche par le sens) sont désactivées par défaut ; ne jamais toucher aux bases d'autres modules (`wa_archive` intouchée par l'IA et la recherche par le sens).

---

## Versions

| Version | Contenu | État |
|---------|---------|------|
| 1.0 – 1.7 | Import, conversation unifiée, période, multi-import, doublons visuels, repliage, options avancées, structure hiérarchique (localStorage) | ✅ |
| 1.8 – 1.8.8 | IndexedDB, rendu lazy, performances gros volumes, panneau masquable, aide, exports multi-formats, import JSON | ✅ 1.8.8 = stable de référence |
| 1.9 – 1.9.2 | Tentatives « Voir tout ce jour » / navigation jour (abandonnées, reprises en 2.0.0) | ❌ |
| 1.9.3 – 1.9.8 | Easter egg, seuils d'avertissement, réglages, détection des anciennes bases (Firefox), nom de fichier fixe | ✅ |
| 2.0.0 – 2.0.1 | Visualiseur de jour depuis la recherche, précédent / suivant, « Voir toute la conversation », « Aller à ce jour » | ✅ |
| 2.1.0 – 2.1.2 | Polices et sql.js intégrés (0 requête externe), thème clair, choix de la police, correctifs téléphone, résultat précédent / suivant | ✅ |
| 2.2.0 | IA optionnelle : fournisseurs Ollama / Mistral / Claude, résumé d'une période, clé au choix | 🟢 testée |
| 2.2.1 | Panneau IA : rendu Markdown, export, jauge de jetons | ✅ validée |
| 2.2.2 | Calendrier de période à cases à cocher ; test de connexion, liste des modèles, message CORS précis | 🟢 testée |
| 2.2.3 – 2.2.5 | Mode « Conversations / IA », historique conservé, cartes pliables, progression et temps estimé, correctif réponses longues | 🟢 testées |
| 2.2.6 | Import / export zip, nom du fichier d'export | 🟢 |
| 2.2.7 | Demandes IA suspendables et reprenables (journal de rejeu, base `waav_ia`) | 🟢 pause / reprise validées |
| 2.3.0 | Recherche ciblée : mots-clés proposés par l'IA, recherche et comptes exacts par WAAV, passages envoyés, dates cliquables | 🟢 testée, retours |
| 2.4.0 | Recherche par le sens (option), mots-clés améliorés, interrupteur sous « Envoyer » | 🟡 retours : aide aux mots-clés faible |
| 2.4.1 | Aide aux mots-clés pertinente : nettoyage, consigne thématique, mots fréquents grisés, « Compléter avec l'IA », suggestions par le sens, fautes de frappe | 🟡 livrée, à essayer |
| 2.4.2 | **Dissociation recherche / statistiques / IA** : recherche avancée sans IA, onglet Stats, orientation des questions de calcul, avertissement d'échantillon | 🟡 livrée, à essayer |

---

## Détail des versions 2.2 à 2.4 (condensé)

- **IA (2.2.x)** : `aiCall` (format compatible OpenAI pour Ollama et Mistral, format Anthropic avec en-tête d'accès navigateur) ; découpage en parties (taille dérivée de la fenêtre de contexte) puis fusion par étapes ; 429 : attente (`retry-after`) et nouvelle tentative ; régulation par jetons/minute et délai ; estimation de durée (latence apprise) ; historique `waav_ai_hist` ; blocs `<think>` retirés. Mode IA = classe `ai-mode` sur `#main` (`#ai-view`), bloc d'informations condensé dans le volet gauche.
- **Reprise (2.2.7)** : chaque réponse de l'IA est enregistrée dans l'ordre ; à la reprise le traitement est rejoué depuis le début, les étapes enregistrées étant servies sans requête (la taille de fusion `chunk` est enregistrée pour garder le même découpage). Export d'état = zip JSON (`waavAiJob`), l'import accepte aussi l'instantané brut de la 2.2.6.
- **Zip / noms (2.2.6)** : `zipEntries` / `zipRead` / `expandZips` (lecture du répertoire central par `slice`, `DecompressionStream`) ; `zipBlob` (CRC-32, deflate par `CompressionStream`) ; `downloadBlob` / `saveFile` gèrent nom libre et zip.
- **Recherche ciblée (2.3.0 – 2.4.0)** : `_aiTarget` (cartes mots-clés › résultats › décision) ; `aiKwRegex` (début de mot, accents et casse ignorés, guillemets = exact) ; passages = résultat ± 2 messages fusionnés (`aiMergeWins`) ; « garder les N meilleurs » = sélection tournante par mois (`aiPickWindows`) ; facts calculés par WAAV placés dans la consigne ; dates de la réponse transformées en liens (`aiLinkDates`). 2.4.0 : comptes sur les pastilles (`aiKwCount`), mots proches (`aiSimilarWords`), mots associés (`aiAssocWords`), source Mots-clés / Sens / Les deux.
- **Recherche par le sens (2.4.0)** : modèles `Xenova/multilingual-e5-small` (≈ 135 Mo, préfixes `query: ` / `passage: `) et `rolf-mozilla/french-embedding-e5-distilled` (≈ 30 Mo) via transformers.js 3.8.1 (jsDelivr) dans un Worker module (repli dans la page) ; `semBuild` (groupes de ≈ 5 messages, lots de 128, pause / reprise, empreinte de l'archive), `semRank`, `semQueryWins`, zone de recherche « Mots | Sens », fenêtre de résultats.
- **Aide aux mots-clés (2.4.1)** : `aiCleanKw` (après `aiParseKw` : ponctuation, apostrophes, mots < 3 lettres, `AI_STOP` étendu aux verbes courants, pluriels regroupés par préfixe, `have` = mots déjà gardés) ; `AI_KWTH` (`pct` 0,05 et `min` 300 : un mot est « très fréquent » s'il dépasse **les deux** seuils) → `T.off` / `T.seen` / `T.parked` (mots mis de côté : grisés, un clic bascule ; non cherchés ; restaurés par `aiKwBack`) ; les mots ajoutés à la main ne sont jamais grisés ; `aiTargetComplete` (« Compléter avec l'IA », nouveaux mots seulement) ; `aiSemSuggest` (`semQueryWins` → `aiAssocWords`, automatique si source ≠ Mots-clés, sinon bouton « Suggérer par le sens ») ; `aiSimilarWords` limité aux fautes (≥ 6 lettres, 3 premières lettres identiques, Damerau-Levenshtein ≤ 1 ou 2 si ≥ 9 lettres, forme plus rare que le mot sauf mot à 0 occurrence). Non appliqué en mode « Automatique » : les comptes n'existent pas avant la recherche, donc rien n'est grisé.
- **Dissociation recherche / statistiques / IA (2.4.2)** — constat sur 471 685 messages : « Qui a commencé le plus souvent les discussions du jour ? » est un calcul, pas une recherche ; l'ancien tuyau (mots devinés par l'IA → 22 passages sur 20 031 → conclusion) donnait une réponse sans valeur. Trois outils : **(A) recherche sans IA** (`stMatcher` : casse `searchOpts.cs`, mot entier `ww`, jokers `*` `?`, ET implicite / OU `|` / SAUF `-`, `"expression"`, `/regex/`, insensible aux accents via `m._n` ; remplace tous les `includes` de `computeFiltered`, du surlignage `hlSearch`, de la bulle et du dépliage des jours ; ne cherche plus dans le nom de l'auteur ; ligne `#search-sum` avec « Analyser avec l'IA » / « Statistiques ») ; **(B) statistiques exactes** (`#stats-view`, `stBase` / `stAnalyse` / `stRender` / `stCount` : début de discussion après un silence réglable `_stGap`, premier message du jour, dernier avant silence, temps de réponse, activité par heure, comptage d'une expression par personne et par année) ; **(C) IA sur la sélection** : la sélection de travail = `filteredMsgs` (période + participants + recherche) ; `stLooksCalc` détecte les questions de calcul dans `aiRun` et propose les statistiques ; quand `wins.length<R.nwin`, l'invite avertit l'IA qu'il s'agit d'un extrait. Le flux « Recherche ciblée » (mots devinés par l'IA) reste disponible mais n'est plus la seule voie : on peut chercher soi-même puis envoyer la sélection (interrupteur « Recherche ciblée » désactivé). Reste à faire si besoin : source « Ma recherche » dans la recherche ciblée, échantillon réparti dans le temps.
- **Vérifications faites** : syntaxe JS, `<div>` équilibrés, suites Playwright (voir `tests-local/`) avec faux serveur d'IA et **faux modèle** d'embeddings. **Non testé** : vrai modèle et vrai moteur (le bac à sable de développement n'accède pas à jsDelivr ni à Hugging Face), Worker module depuis GitHub Pages / `file://`, vitesse réelle d'indexation, qualité en français, Firefox récent, Windows 10 / 11, Linux, mémoire sur 400 000+ messages.

---

## Décisions et principes à ne pas remettre en cause

1. **Ne pas coder sans l'accord de l'utilisateur** (proposer, expliquer, attendre le feu vert ; une question à la fois).
2. **Économiser les crédits** ; demander avant toute action consommatrice.
3. Une demande fonctionnelle = une version ; incrémenter partout (en-tête du fichier, `<title>`, aide, pastille, `APP_VERSION`, changelog intégré avec badge ACTUEL) et mettre à jour ce document.
4. **Recherche ciblée** : l'IA propose les mots-clés à partir de la **question seule** (jamais des messages), l'utilisateur valide (réglage « Automatique » possible) ; WAAV cherche et compte lui-même ; contexte ± 2 messages ; s'il y a trop de résultats, demander à chaque fois (meilleurs / tout / affiner) ; la recherche porte sur la **sélection actuelle**.
5. **Recherche par le sens** : autonome (pas d'Ollama), en option, avec information claire avant téléchargement, utilisable **sans IA** ; les comptes exacts viennent des mots-clés, jamais du sens.
6. Interface IA : commutateur « Conversations | IA » (les trois volets côte à côte ont été **abandonnés**) ; boutons et messages d'état dans la **carte** de la demande (pas sous la zone de saisie) ; exports IA `.md` non concernés par le nom de fichier libre.
7. Une seule demande en pause à la fois ; WAAV ne travaille que page ouverte.

### Fonctionnalités abandonnées
« Voir tout ce jour » et navigation jour (1.9–1.9.2, repris en 2.0.0) ; trois volets côte à côte (2.4.0, jugé peu utile) ; emoji récents sous Windows 7 : décision de ne rien changer.

---

## Roadmap

| Version | Contenu | État |
|---------|---------|------|
| 2.4.1 | Aide aux mots-clés pertinente (voir ci-dessus) | Livrée, à essayer sur de vrais mots-clés |
| 2.4.2 | Recherche / statistiques / IA dissociées (voir ci-dessus) | Livrée, à essayer |
| Ensuite | Validation de la recherche par le sens sur les PC de l'utilisateur (Windows 10 / 11, Linux) avec le bouton « Tester le modèle » ; ajustements selon vitesse et qualité | À faire |
| Plus tard | Marque-pages, affichage des médias, synchronisation MySQL (API PHP : jeton, HTTPS), éventuellement analyse du ton | À planifier |

---

## Bonnes pratiques établies

1. Partir de la dernière version livrée ; **ne jamais patcher un fichier déjà patché plusieurs fois sans contrôle** ; modifier par scripts Python avec assertions (chaque remplacement doit trouver exactement une occurrence).
2. Vérification systématique : `new Function(js)` sur les scripts, `<div>` équilibrés, puis tests Playwright sur une conversation **fictive** (jamais de vraies conversations), bureau **et** mobile (390 px), avec une **longue réponse** pour tout élément qui défile.
3. Attention : un remplacement global de la chaîne de version (`vX.Y.Z`) renomme aussi les commentaires et ancres de code qui la contiennent.
4. En usage local, nommer le fichier `waav.html` et le garder dans le même dossier (stockage lié au chemin sous Firefox).
5. Indiquer clairement ce qui n'a pas pu être testé.

---

## Contexte technique — export MySQL

| Paramètre | Valeur |
|-----------|--------|
| Base cible | MySQL / MariaDB (phpMyAdmin) |
| Jeu de caractères | `utf8mb4` ; interclassement `utf8mb4_unicode_ci` |
| Format | Fichier `.sql` généré par l'export MySQL de WAAV |

---

*Document condensé le 27 novembre 2026 — WAAV v2.4.2*
