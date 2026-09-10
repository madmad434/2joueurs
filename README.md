# Jeux à deux — huit duels en ligne (P2P) ou en local

Collection de huit jeux à deux joueurs, en HTML/CSS/JS pur, **sans installation ni
serveur applicatif**. Chaque jeu est un fichier autonome ; `index.html` n'est
qu'un menu de lancement.

| N° | Jeu | Fichier | Synchronisation |
|----|-----|---------|-----------------|
| 1 | Morpion | `jeu_MorpionEnLigne.html` | coup + état renvoyé par l'hôte |
| 2 | Jeu de dames | `jeu_dames_enligne.html` | coup + état renvoyé par l'hôte |
| 3 | Awalé | `jeu_awale_enligne.html` | coup + état renvoyé par l'hôte |
| 4 | Comète | `jeu_comete_enligne.html` | graine au départ, score à l'arrivée |
| 5 | Météorite | `jeu_meteorite_enligne.html` | graine au départ, score à l'arrivée |
| 6 | Tennis | `jeu_tennis_enligne.html` | temps réel, l'hôte fait autorité |
| 7 | Carrom | `jeu_carrom_enligne.html` | tir transmis, puis même physique des deux côtés |
| 8 | Bataille navale | `jeu_bataillenavale_enligne.html` | tir transmis, résultat annoncé par celui qui le subit |

---

## 1. Démarrer

### En local, tout de suite

Double-cliquez sur `index.html`, choisissez un jeu, puis **🌐 Connexion →
🪑 Jouer en local**. Les deux joueurs se partagent le même écran. Rien d'autre
n'est nécessaire.

### En ligne, à deux machines

Le mode en ligne repose sur WebRTC (bibliothèque PeerJS, chargée depuis un CDN).
Il demande une **connexion Internet**.

Il fonctionne aussi par simple double-clic, en `file://` : la spécification
*Secure Contexts* range le schéma `file` parmi les origines dignes de confiance,
au même titre que `https` et `localhost`. Dans ce cas, le jeu **demande votre
accord** avant de créer ou de rejoindre une partie et rappelle les quatre
contraintes : votre adversaire doit avoir le même fichier sur son poste, le lien
d'invitation ne fonctionne pas — il est d'ailleurs **masqué** dans ce mode, seul
le code à 5 caractères est proposé —, les records
et le réglage du son peuvent ne pas être conservés, et Safari refuse parfois la
connexion. Servir la page en `https://…` lève toutes ces limites.

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

## 2. Les huit jeux

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

### 4 — Comète
Une comète, un seul bouton (clic, tap ou la touche choisie) qui donne une
impulsion vers le haut ; sinon elle tombe. +1 par barrière franchie, vitesse
croissante par paliers à 10, 25, 50 et 100 points. Toucher une barrière ou le sol
fige le score. Les deux joueurs volent **en même temps sur le même parcours** ;
meilleur score gagne la manche, duel au meilleur des trois, égalité rejouée.

Réglages du bandeau :

* **Joueurs** — *2 joueurs (duel)* ou *1 joueur (solo)*. En solo, ni adversaire ni
  connexion : le record est conservé sur l'appareil.
* **Vitesse** — très lente, lente, normale, rapide, très rapide. En duel, la vitesse choisie
  par l'hôte est transmise avec la graine et s'applique aux deux joueurs.
* **Touche d'impulsion** — Espace, ↑, W/Z ou Entrée. Le clic et le tap
  fonctionnent toujours.

### 5 — Météorite
Un personnage sous une pluie de météorites tombant en diagonale. Manche de
90 secondes au plus ; le score est le temps de survie au dixième de seconde.
Difficulté par paliers à 20 s, 45 s et 70 s. Même averse pour les deux joueurs,
meilleur des trois.

Réglages du bandeau :

* **Joueurs** — duel ou solo, avec record conservé sur l'appareil.
* **Vitesse de chute** — de très lente à très rapide ; en duel, le réglage de l'hôte
  s'applique aux deux joueurs.
* **Touches** — *Flèches ← → ↑*, *AZERTY (Q D Z)* ou *QWERTY (A D W)* ; Espace
  saute toujours. Sur téléphone, trois touches tactiles s'affichent sur la scène.

### 6 — Tennis
Tennis des années 80 : la raquette suit la souris ou le doigt (flèches au
clavier). L'hôte est la raquette **gauche**, l'invité la raquette **droite**. La
balle accélère à chaque échange et l'angle de renvoi dépend du point d'impact.
Premier à **10 points**. En local : W / S d'un côté, flèches de l'autre.

Réglages du bandeau :

* **Joueurs** — duel, ou *1 joueur (entraînement)* : la raquette d'en face est
  alors tenue par la machine, qui suit la balle à vitesse limitée et revient au
  centre entre deux échanges.
* **Ma raquette** — à gauche, à droite, en bas ou en haut de **votre** écran ; le
  terrain bascule en portrait pour les deux positions verticales.
* **Format du terrain** — *large (16:9)* ou *compact*. En vue verticale, un
  terrain 16:9 se réduit à un couloir étroit ; le format compact élargit l'aire
  de jeu d'environ cinq centimètres sur un écran d'ordinateur courant.
  Contrairement à l'orientation, ce réglage **concerne les deux joueurs** : celui
  de l'hôte s'applique, transmis au coup d'envoi, sans quoi les deux postes ne
  verraient pas la balle au même endroit.

### 7 — Carrom
Billard indien à deux : 9 pions blancs (hôte) contre 9 noirs (invité), une reine
rouge, un palet. On arme en glissant depuis le palet vers l'arrière et on relâche
pour tirer ; un clic près de sa ligne de base repositionne le palet.

Empocher un des siens rend la main ; un pion adverse empoché est compté pour lui ;
le palet dans un trou est une faute, un de vos pions revient au centre. La reine
doit être couverte dans le même coup ou le suivant, faute de quoi elle retourne
au centre ; couverte, elle vaut 3 points. La manche revient au premier joueur
sans pion sur le plateau.

Réglages du bandeau :

* **Joueurs** — duel, ou *1 joueur (entraînement)* : rentrer ses neuf blancs en un
  minimum de coups, un noir empoché retournant au centre. Record gardé sur l'appareil.
* **Ma ligne de base** — en bas, à gauche, en haut ou à droite de **votre** écran.
* **Puissance du tir** — quatre crans, de *douce* à *très forte*. Pendant la
  visée, une **règle graduée** affiche le pourcentage exact de la puissance
  choisie, du vert au rouge. Elle est tracée dans une bande réservée **sous** le
  plateau, pour ne jamais recouvrir un pion ni un trou.
* **Aide à la visée** — ligne courte, longue, ou aucune.

### 8 — Bataille navale
Chacun place sa flotte sur sa grille, puis on se tire dessus à tour de rôle
jusqu'à couler toute la flotte adverse.

Réglages du bandeau :

* **Joueurs** — duel, ou *1 joueur (entraînement)* : la machine place sa flotte
  au hasard et tire en cherchant autour de ses touches, comme un joueur humain.
* **Grille** — *Simple* 8 × 8, *Moyen* 10 × 10, *Difficile* 12 × 12.
* **Après un touché** — *rejouer* (variante la plus courante) ou *passer la main*
  à chaque tir.
* **🚢 Flotte** — une fenêtre où l'on fixe le nombre de bâtiments de chaque type :
  porte-avions (5 cases), croiseur (4), contre-torpilleur (3), sous-marin (3),
  torpilleur (2), de 0 à 6 chacun. Trois compositions toutes faites, un
  récapitulatif du taux d'occupation, et un avertissement au-delà de 42 % — au
  delà, le placement devient souvent impossible et le lancement est refusé. La
  composition est conservée sur l'appareil.

En duel, la grille et la flotte de **l'hôte** s'appliquent aux deux joueurs.

Placement : on choisit un bâtiment dans la barre, le sens avec **→ Horizontal**
ou **↓ Vertical** (touche **R** pour basculer), puis on clique sur sa grille. Le
survol montre à l'avance l'emplacement retenu, en vert s'il convient et en rouge
sinon ; près d'un bord, la coque recule juste ce qu'il faut pour tenir.
**🎲 Aléatoire** place tout d'un coup, **↺ Recommencer** vide la grille. Les
bâtiments peuvent se toucher, jamais se chevaucher.

À deux sur un seul écran, chacun place sa flotte à son tour et un écran de
passation floute les grilles entre les tours.

#### Orientation du terrain (Tennis et Carrom)

Le réglage est **purement local**. La partie se déroule toujours dans un repère
canonique — hôte à gauche au Tennis, ligne de l'hôte en bas au Carrom — et seuls
l'affichage et la saisie passent par une rotation. Les deux joueurs peuvent donc
voir le terrain différemment, y compris de part et d'autre d'une tablette posée à
plat, sans aucun risque de divergence entre les deux postes.

---

## 3. Interface commune

Tous les jeux partagent la même charte graphique et la même disposition :

* **Bandeau de configuration** — titre, réglages du jeu, pseudo, puis les boutons
  communs :

  | Bouton | Rôle |
  |---|---|
  | `▶ Lancer` | démarre une manche (réservé à l'hôte en ligne) |
  | `🌐 Connexion` | créer, rejoindre, ou passer en local |
  | `📜 Historique` | relevé de la partie dans une fenêtre, avec `🖨 Imprimer` |
  | `🔊 Son` / `🔇 Muet` | coupe ou rétablit les effets sonores, choix mémorisé pour les huit jeux |
  | `⛶ Plein écran` | agrandit le jeu à tout l'écran, ce qui écarte les info-bulles de la barre des tâches ; Échap rétablit la fenêtre, rien n'est modifié durablement |
  | `❓ Aide` | règles et fonctionnement réseau du jeu |
  | `⟲ Réinitialiser` | arrête la partie en cours sans quitter la page |
  | `✕ Quitter` | revient au menu |
* **Bandeau réseau** — état de la liaison, code de salle, lien d'invitation,
  latence mesurée.
* **Barre de statistiques** — cinq indicateurs propres au jeu.
* **Panneau latéral** — les deux joueurs, leur score en manches et leurs **gains
  en cours de manche** (graines à l'Awalé, pions et reine au Carrom, points au
  Tennis, barrières ou temps de survie à Comète et Météorite, bâtiments coulés à
  la Bataille navale, pièces restantes aux Dames, coups joués au Morpion), plus une discussion avec réactions
  rapides. Masqué sous 900 px.
* **Historique** — il n'occupe plus le panneau latéral : il s'ouvre en fenêtre par
  le bouton `📜 Historique`, et la fenêtre de fin de manche propose le même
  relevé, imprimable. Chaque ligne porte le **nom du joueur** concerné, et la
  fenêtre rappelle en en-tête les deux adversaires (ou le joueur seul en solo) ;
  cet en-tête est repris dans la version imprimée.
* **Son** — effets synthétisés à la volée (WebAudio), aucun fichier audio :
  pose d'un pion, semis et capture, rebond et point, décompte, victoire ou
  défaite. L'état actif/muet est partagé par les huit jeux.
* **Fenêtres modales** — connexion, aide détaillée, fin de manche.
* Touche **Échap** : ferme toute fenêtre ouverte.
* **Clic droit** : le menu contextuel du navigateur est neutralisé dans les huit
  jeux, où il gênait la partie (« Ouvrir dans un nouvel onglet »,
  « Enregistrer l'image… »). Il reste actif là où il sert vraiment : champs de
  saisie, code de la salle, lien d'invitation, messages et historique — pour
  copier et coller normalement.

---

## 4. Notes techniques

### Trois familles de synchronisation

| Famille | Messages échangés |
|---------|-------------------|
| Morpion, Dames, Awalé | `{"type":"coup", …}` puis un état complet renvoyé par l'hôte. Les deux postes appliquent la même fonction de règles : l'état reste identique des deux côtés. |
| Comète, Météorite | `{"type":"seed","seed":…,"mul":…}` au lancement de la manche (graine **et** réglage de vitesse), `{"type":"score","value":…}` à la mort. Rien entre les deux. |
| Tennis | Temps réel : l'invité envoie `{"type":"pos","y":…}` ~30 fois/s ; l'hôte, seul arbitre, diffuse `{"type":"ball", …}`. L'invité ne simule rien. |

### Déterminisme des jeux d'action

Contrat respecté par Comète et Météorite :

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
* Tennis : plus de dix secondes sans nouvelle de l'adversaire → message d'attente ;
  raquette adverse lissée pour absorber la latence.
* Awalé : coup affamant refusé côté émetteur, donc jamais transmis ; position
  bloquée résolue par partage du plateau.

### Organisation des fichiers

    index.html                        menu (6 tuiles, aide, mode d'emploi du duel)
    jeu_MorpionEnLigne.html           jeu 1
    jeu_dames_enligne.html            jeu 2
    jeu_awale_enligne.html            jeu 3
    jeu_comete_enligne.html           jeu 4
    jeu_meteorite_enligne.html        jeu 5
    jeu_tennis_enligne.html           jeu 6
    jeu_carrom_enligne.html           jeu 7
    jeu_bataillenavale_enligne.html   jeu 8
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
| En `file://`, l'adversaire ne trouve pas la salle | Il n'a pas le fichier, ou le lien d'invitation a été envoyé au lieu du code | Chacun ouvre sa copie du fichier ; ne transmettre que le code à 5 caractères |
| Connexion impossible au bureau | VPN ou pare-feu d'entreprise filtrant WebRTC | Essayer en partage de connexion mobile |
| Panneau latéral absent | Fenêtre de moins de 900 px | Élargir la fenêtre, ou continuer sans (facultatif) |
| Aucun son | Bouton sur `🔇 Muet`, ou onglet jamais cliqué | Cliquer sur `🔊 Son` ; les navigateurs n'autorisent le son qu'après une première interaction |
| La fenêtre d'impression ne s'ouvre pas | Bloqueur de fenêtres surgissantes | Autoriser les fenêtres pour ce site |
