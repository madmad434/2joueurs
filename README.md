# Jeux à deux — six duels en ligne (P2P) ou en local

Collection de six jeux à deux joueurs, en HTML/CSS/JS pur, **sans installation ni
serveur applicatif**. Chaque jeu est un fichier autonome ; `index.html` n'est
qu'un menu de lancement.

| N° | Jeu | Fichier | Synchronisation |
|----|-----|---------|-----------------|
| 1 | Morpion | `jeu_MorpionEnLigne.html` | coup + état renvoyé par l'hôte |
| 2 | Jeu de dames | `jeu_dames_enligne.html` | coup + état renvoyé par l'hôte |
| 3 | Awalé | `jeu_awale_enligne.html` | coup + état renvoyé par l'hôte |
| 4 | Flappy Comète | `jeu_flappycomete_enligne.html` | graine au départ, score à l'arrivée |
| 5 | Comet Survival | `jeu_cometsurvival_enligne.html` | graine au départ, score à l'arrivée |
| 6 | Pong Néon | `jeu_pongneon_enligne.html` | temps réel, l'hôte fait autorité |

---

## 1. Démarrer

### En local, tout de suite

Double-cliquez sur `index.html`, choisissez un jeu, puis **🌐 Connexion →
🪑 Jouer en local**. Les deux joueurs se partagent le même écran. Rien d'autre
n'est nécessaire.

### En ligne, à deux machines

Le mode en ligne repose sur WebRTC (bibliothèque PeerJS, chargée depuis un CDN).
Il demande donc :

* une **connexion Internet** ;
* une page servie en **`https://…`** — WebRTC est bloqué sur `file://` dans
  plusieurs navigateurs.

Puis, dans le jeu :

1. Un joueur ouvre **🌐 Connexion → 🎬 Créer une partie** : un code de cinq
   caractères et un lien d'invitation s'affichent.
2. Il transmet le code (ou le lien) à l'autre joueur.
3. L'autre ouvre **🌐 Connexion → 🔑 Rejoindre une partie** et saisit le code.
   Avec le lien, le code est déjà pré-rempli.
4. L'**hôte** lance les manches avec **▶ Lancer la manche**.

La liaison est directe d'un navigateur à l'autre : aucune donnée de partie ne
transite par un serveur, seul l'annuaire public PeerJS sert à la mise en relation.

### Essayer « comme en ligne » sur son poste

    python3 -m http.server 8000

puis `http://localhost:8000`. Deux onglets sur la même machine suffisent pour
tester à deux. Pour deux machines distinctes, il faut une vraie adresse
`https://…`.

Deux raccourcis sont fournis : `demarrer-serveur-local.cmd` (Windows) et
`demarrer-serveur-local.sh` (macOS / Linux).

### Publier

Envoyez **le contenu** de ce dossier (et non le dossier lui-même) sur un dépôt
GitHub public, de sorte que `index.html` soit à la racine. Puis :
**Settings → Pages**, source *Deploy from a branch*, branche `main`, dossier
`/ (root)`. Tous les chemins sont relatifs : l'adresse du dépôt n'a aucune
importance.

---

## 2. Les six jeux

### 1 — Morpion
Aligner ses symboles avant l'adversaire, grille 3×3 ou 4×4, alignement de 3 ou 4,
cinq jeux de symboles. Score cumulé par manches, le joueur qui commence alterne.

### 2 — Jeu de dames
Le damier classique : prises obligatoires, rafles, promotion en dame.

### 3 — Awalé
Plateau de 12 cases, 48 graines (4 par case), un grenier par joueur.
L'hôte joue la rangée du **bas** et commence.

* On sème toutes les graines d'une de **ses** cases, une par une, dans le sens
  **antihoraire**. À partir de 12 graines en main, la case de départ est sautée.
* **Capture** : si la dernière graine tombe dans une case adverse qui atteint
  2 ou 3 graines, cette case est récoltée, puis la récolte se poursuit en chaîne
  vers la case précédente tant qu'elle est adverse et contient 2 ou 3 graines.
* **Famine** : un coup qui laisserait l'adversaire sans aucune graine est
  interdit s'il existe un coup qui le nourrit. Les coups interdits ne sont pas
  proposés (la case n'est pas surlignée et le clic est refusé) : ils ne sont donc
  jamais transmis sur le réseau.
* Si aucun coup ne nourrit l'adversaire, le joueur récolte toutes les graines
  restantes et la partie s'arrête. Si un camp est vide, l'autre joueur récolte
  le plateau.
* **Fin** : premier à 25 graines, ou grenier le plus plein ; 24 — 24 est nul.
  Une position bloquée (60 coups sans capture) est partagée, chacun récoltant
  son camp.
* Le semis est animé, graine par graine, à environ 80 ms par graine.

### 4 — Flappy Comète
Une comète, un seul bouton (clic, tap ou **Espace**) qui donne une impulsion vers
le haut ; sinon elle tombe. +1 par barrière franchie, vitesse croissante par
paliers à 10, 25, 50 et 100 points. Toucher une barrière ou le sol fige le score.
Les deux joueurs volent **en même temps sur le même parcours** ; meilleur score
gagne la manche, duel au meilleur des trois, égalité rejouée.

### 5 — Comet Survival
Un personnage (← → pour se déplacer, ↑ pour sauter ; trois touches tactiles sur
téléphone) sous une pluie de comètes tombant en diagonale. Manche de 90 secondes
au plus ; le score est le temps de survie au dixième de seconde. Difficulté par
paliers à 20 s, 45 s et 70 s. Même averse pour les deux joueurs, meilleur des trois.

### 6 — Pong Néon
Tennis des années 80 : la raquette suit la souris ou le doigt (↑ / ↓ au clavier).
L'hôte est la raquette **gauche**, l'invité la raquette **droite**. La balle
accélère à chaque échange et l'angle de renvoi dépend du point d'impact. Premier
à **10 points**. En local : W / S à gauche, ↑ / ↓ à droite.

---

## 3. Interface commune

Tous les jeux partagent la même charte graphique et la même disposition :

* **Bandeau de configuration** — titre, réglages du jeu, pseudo, `▶ Lancer`,
  `🌐 Connexion`, `❓ Aide`, `🏠 Menu`, `✕ Quitter`.
* **Bandeau réseau** — état de la liaison, code de salle, lien d'invitation,
  latence mesurée.
* **Barre de statistiques** — cinq indicateurs propres au jeu.
* **Panneau latéral** — les deux joueurs et leur score, une discussion avec
  réactions rapides, l'historique des coups ou des scores. Masqué sous 900 px.
* **Fenêtres modales** — connexion, aide détaillée, fin de manche.
* Touche **Échap** : ferme toute fenêtre ouverte.

---

## 4. Notes techniques

### Trois familles de synchronisation

| Famille | Messages échangés |
|---------|-------------------|
| Morpion, Dames, Awalé | `{"type":"coup", …}` puis un état complet renvoyé par l'hôte. Les deux postes appliquent la même fonction de règles : l'état reste identique des deux côtés. |
| Flappy Comète, Comet Survival | `{"type":"seed","seed":…}` à la connexion, `{"type":"score","value":…}` à la mort. Rien entre les deux. |
| Pong Néon | Temps réel : l'invité envoie `{"type":"pos","y":…}` ~30 fois/s ; l'hôte, seul arbitre, diffuse `{"type":"ball", …}`. L'invité ne simule rien. |

### Déterminisme des jeux d'action

Contrat respecté par Flappy Comète et Comet Survival :

* générateur pseudo-aléatoire **mulberry32** — même graine, même séquence ;
* simulation à **pas fixe** (60 pas par seconde de temps écoulé, jamais image par
  image), avec un plafond de rattrapage : un PC et un téléphone parcourent
  exactement le même terrain ;
* toutes les positions en **unités relatives** (1 = hauteur du terrain), jamais
  en pixels.

### Cas limites traités

* Déconnexion en cours de jeu : message, puis victoire par forfait pour l'autre
  joueur (jeux d'action) ou arrêt de la partie (Pong, jeux de plateau).
* Attente d'un score adverse : deux minutes au maximum, puis forfait.
* Scores égaux : manche rejouée.
* Pong : plus de dix secondes sans nouvelle de l'adversaire → message d'attente ;
  raquette adverse lissée pour absorber la latence.
* Awalé : coup affamant refusé côté émetteur, donc jamais transmis ; position
  bloquée résolue par partage du plateau.

### Organisation des fichiers

    index.html                        menu (6 tuiles, aide, mode d'emploi du duel)
    jeu_MorpionEnLigne.html           jeu 1
    jeu_dames_enligne.html            jeu 2
    jeu_awale_enligne.html            jeu 3
    jeu_flappycomete_enligne.html     jeu 4
    jeu_cometsurvival_enligne.html    jeu 5
    jeu_pongneon_enligne.html         jeu 6
    demarrer-serveur-local.cmd        serveur de test (Windows)
    demarrer-serveur-local.sh         serveur de test (macOS / Linux)
    README.md                         ce fichier

Aucun dossier `assets/` : chaque fichier est autonome. La seule dépendance
externe est **PeerJS 1.5.4**, chargée depuis un CDN (avec deux CDN de secours) et
utilisée uniquement pour le mode en ligne.

### Compatibilité

Navigateurs récents pour ordinateur, tablette et téléphone (Chrome, Edge,
Firefox, Safari). Les jeux d'action utilisent `<canvas>` et des événements
*pointer*, donc souris comme tactile.

---

## 5. Dépannage

| Symptôme | Cause probable | Solution |
|----------|----------------|----------|
| « Impossible de charger PeerJS » | Pas d'accès Internet, ou CDN bloqué | Vérifier la connexion ; réessayer |
| « Salle introuvable » | Code erroné, ou hôte ayant rechargé sa page | Recréer une partie, retransmettre le code |
| Bouton `▶ Lancer` grisé | Vous êtes l'invité, ou l'adversaire n'est pas encore connecté | Seul l'hôte lance les manches |
| Rien ne se connecte en `file://` | WebRTC bloqué hors HTTPS | Passer par un serveur local ou publier le dossier |
| Connexion impossible au bureau | VPN ou pare-feu d'entreprise filtrant WebRTC | Essayer en partage de connexion mobile |
| Panneau latéral absent | Fenêtre de moins de 900 px | Élargir la fenêtre, ou continuer sans (facultatif) |
