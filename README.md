# ProjetPacNam

## Se familiariser avec l'environnement

Vous êtes sur i3 : un gestionnaire de fenêtres qui marche par tuile. Fini les fenêtres flottantes de Windows !

- `windows + D`      (pour toutes recherche d'application)

- `windows + enter`  (ouvre un nouveau terminal)

Une fois le terminal lancé, pour afficher ce qu’il y a dans le dossier courant, faites `ls` ou `l`

Pour changer de dossier faites `cd "Nom du dossier"`

Pour revenir en arrière faites `cd ..`

Maintenant que vous avez les cartes en main, on peut y aller !

Pour commencer, exécutez la commande `./setup.sh` qui permettra d'installer l'environnement.

Une fois que la commande est terminée, pour lancer Visual Studio Code tapez `code` dans le terminal.

Pour lancer votre programme python aller dans le dossier `PacNam/` et exécutez `python main.py`

Vous avez les fichiers `pacnam.py` et `ghost.py` à compléter.

## Le fichier `pacnam.py`

Vous avez les attributs suivant à votre disposition :

- `self.size`         (Taille en pixel d'une case de la grille)

- `self.speed`        (Vitesse du PacNam)

- `self.life`         (Points de vie du PacNam)

- `self.dt`           (Le delta de temps, pour adapter le mouvement à la fréquence d'image (vous n'avez pas à le modifier))

- `self.pos.*`       (Position x (self.pos.x) et y (self.pos.y) du PacNam sur la grille)

- `self.start_pos`    (Position de départ du PacNam (utile pour le mettre à sa position de départ))

- `self.score`        (Score du PacNam)

- `self.power_up`     (Vous dit si PacNam a son power_up (True) ou pas (False))

- `self.timer`        (Durée de recupération du power-up)

- `self.make.maze`    (Un tableau en deux dimensions (une liste de listes) qui représente les murs (1) et les espaces ou PacNam peux aller (0))

**Note** : Pour accéder à une case en particulier, vous pouvez faire ```python self.maze.maze[ligne][colonnes]```

- `self.maze.points`  (Même structure que précédemment, mais qui représente les points (1) les power-ups (2) et les espaces vides (ni points ni power-up) (0))

Dans ce fichier, vous devrez faire les méthodes :

- `move_*()`         (Permet au PacMan de bouger à gauche/droite/haut/bas dans le labyrinthe en vérifiant qu’il ne va pas dans un mur. Vous devez modifier les variables `self.pos.x` et `self.pos.y`. *Vous devrez utiliser self.dt dans cette fonction en la multipliant par la vitesse du PacNam*)

- `kill()`            (PacNam s'est fait toucher ! Vous devez lui enlever une vie et le remettre à sa position de départ)

- `eat()`             (Permet à PacNam de manger les points et incrémenter son score et mettre à jour son power_up)

- `power()`           (Permet à PacNam de garder son power_up pendant 10 secondes. (self.timer sera utilisé ici)

## Le fichier `maze.py`

Il manque la méthode `check_end()` qui renvois `False` si tous les points ont été mangé par PacNam (ou sinon `True`)

## Le Fichier `ghost.py`

*Les attributs `self.size`, `self.maze.maze`, `self.maze.points`, `self.speed` et `self.dt` représente la même chose que dans `pacnam.py`*

Il nous reste donc : 

- `self.pos`          (Liste de valeurs `[x, y]` qui correspond aux coordonées en pixels du fantôme)

- `self.real_pos`     (Tuple de valeurs `(x, y)` qui correspond aux coordonées sur la grille du `self.maze.maze`)

- `self.path`         (Liste de tuples représentant des coordonnées sur la grille que le fantôme doit suivre pour atteindre sa cible. Cette liste pourra être générée par un algorithme que l'on expliquera plus bas)

*La méthode `move_*()` étant la même que pour PacNam, voici les autres méthodes :*

- `kill`              (Le Fantôme a été touché, quand PacNam avais sont power_up. Il doit retourner à son point de départ et son chemin a été réinitialisé)

- `action()`          (Soit vous faites se déplacer le fantôme au hasard sur la grille, soit vous lui faites suivre l'algorithme A*)


### Algorithme A*

A* est un algorithme qui est souvent utilisé dans les systèmes d'IA. C'est un algorithme de recherche qui permet de planifier la trajectoire optimale vers une cible dans un espace défini.
Ce n'est donc pas une IA en tant que telle, une IA inclut des capacités d'apprentissage et d'adaptation en temps réel, comme c'est le cas avec les réseaux de neurones ou les systèmes d'apprentissage.
A* est un algorithme qui utilise des règles définies à l'avance et ne modifie pas son comportement en fonction de l'expérience ou de nouvelles informations.

 #### Fonctionnement général

A* calcule deux choses pour chaque position :

   - **Le coût pour y arriver depuis la position de départ** `g`.
   - **Une estimation de la distance restante jusqu’a la position cible** `h`.

On additionne ces deux valeurs et on obtient `f = g + h`. À chaque étape, l'algorithme sélectionne la case avec la plus petite valeur `f` pour continuer son chemin.
Il cherche le chemin pour se rapprocher de la cible, mais aussi le chemin le plus efficace.


#### Implémentation de l'algorithme A*

On va coder la méthode `Ghost.astar(maze, start, end)` pour générer le plus court chemin d'un point de départ `start` vers une cible `end`, Cela pourrait être directement Pac-Man, mais cela rendrait le jeu trop difficile.
La methode qui vous est fournie `Ghost.find_pos()`, vous renvoie une position valide aléatoire dans la grille.

##### Étape 1 : Initialisation des Variables

- Un **tas** pour stocker les positions à explorer. Pour faire simple, un tas est une structure de données qui maintient les éléments triés.
   On utilise une bibliothèque pour gérer ce tas plus facilement.
  ```python
  heap = []

  #Utilisation
  heapq.heappush(heap,(f,node)) # Ajoute le tuple (f,node) dans heap
  f, node = heapq.heappop(heap) # On affecte à f le coût le plus faible dans le tas et à node le nœud qui lui est associé.
  ```
  
- Un **ensemble** pour stocker les cases visitées, cela permet de stocker des éléments sans doublons.
  ```python
  visited = set() # Cree un ensemble vide

  #Utilisation
  visited.add(node) # Ajoute un node a l'ensemble, s'il est deja dedans il sera ignore
  if node in visited: # On teste l'appartenance d'un noeud comme pour une liste classique
     # Instructions
  ```
- Un **dictionnaire** pour garder une trace des coûts `g` pour atteindre chaque position depuis le point de départ.
   ```python
   costs = {start : 0} # On y initialise la case d'où l'on part, qui a donc un coût de 0.
   ```
- Un **dictionnaire `parents`** pour garder une trace du chemin parcouru, c’est-à-dire de quelle case on est venu pour arriver à chaque case.
   ```python
   parents = {}
   ```

##### Étape 2 : Définition de `g` et `h`

- `g` représente la distance depuis le départ jusqu’à une position.
- `h` représente la distance directe entre la position actuelle et la cible. La formule est celle de la distance entre deux points.
  ```python
  h = math.sqrt((x - end[0]) ** 2 + (y - end[1]) ** 2)
  ```
##### Étape 3 : Boucler pour Explorer les Positions

**Boucle principale** (tant qu’il reste des positions dans le tas) :
   - Prendre la position avec le plus faible cout, la plus petite valeur `f`.
   - Si cette position **n’a pas été visitée** :
     - Marquer cette position comme visitée en l'ajoutant à `visited`.
     - **Si c'est la case cible** :
       - Remonter dans `parents` pour construire la liste du chemin.
       - Retourner la liste trouvée.

   - **Pour chaque voisin** (haut, bas, gauche, droite) de la position actuelle :
     - **Vérifier** que le voisin est dans les limites du labyrinthe et n'est pas un mur.
        - Calculer le nouveau coût `g` pour atteindre ce voisin, c'est donc le cout de la case actuelle + 1.

        - **Si ce coût est inférieur** au coût précédemment enregistré  dans `costs` ou **que la case n'était pas enregistrée** :
          - Mettre à jour les valeurs de `g`, `h`, et `f` pour ce voisin.
          - Enregistrer la case actuelle comme parent de ce voisin dans `parents`.
          - Ajouter le voisin a `heap` pour qu’il soit exploré plus tard.

Si la boucle s'est terminée sans rien retourner, c'est qu'aucun chemin n'a été trouvé, on renvoie donc une liste vide.



