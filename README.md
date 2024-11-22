# Compte-rendu-TP-BDD-NoSQL---INFO3 (Partie 1)


Redis est une base de données NoSQL de type clé-valeur, on l'utilise pour manipulations rapides en mémoire et pouvant aussi persister des données sur disque.
Il stocke des informations sous forme de **paires clé-valeur**. Chaque donnée est associée à une "clé" unique. 
Redis est souvent utilisé pour stocker des informations temporaires, comme le nombre de visites d’un site web ou des sessions utilisateurs.

## Démarrage
Pour commencer à utiliser Redis, on lance le **serveur Redis** à l’aide de la commande :

```bash
redis-server
```

Ensuite, pour communiquer avec Redis, un **client CLI** est utilisé :

```bash
redis-cli
```

Ce client est comme une console qui permet d'envoyer des commandes au serveur pour interagir avec la base de données.

## Commandes CRUD
Redis permet d'effectuer des opérations **CRUD** :

- **Create** : Créer une clé avec une valeur associée. Par exemple :
  
  ```bash
  SET demo "bonjour"
  ```

  Crée une clé nommée "demo" ayant pour valeur "bonjour".

- **Read** : Lire la valeur d’une clé avec la commande :

  ```bash
  GET demo
  ```

  Cette commande retourne "bonjour".

- **Update** : Modifier la valeur d'une clé existante :

  ```bash
  SET demo "salut"
  ```

  Cette commande met à jour la valeur de la clé "demo".

- **Delete** : Supprimer une clé :

  ```bash
  DEL demo
  ```

  Supprime la clé "demo".

## Persistance des Données
Redis stocke les données en **RAM** pour des accès rapides. Pour éviter la perte de données en cas de panne, il propose des options de persistance sur disque avec des configurations spécifiques.

## Gestion des Compteurs
On utilise souvent Redis pour des tâches comme **compter des visiteurs** sur un site web. Pour cela :

- On définit une clé avec un compteur initial à 0 :

  ```bash
  SET date 0
  ```

- Ensuite, on peut incrémenter cette valeur :

  ```bash
  INCR date
  ```

  Cela incrémente le compteur à chaque nouvelle visite.

Redis gère aussi la **concurrence**, c’est-à-dire les situations où plusieurs utilisateurs se connectent simultanément et essayent de modifier la même valeur.

## Durée de Vie des Clés
On peut définir une **durée de vie pour une clé** avec la commande `EXPIRE` :

```bash
EXPIRE ma_cle 120
```

Cette commande indique que la clé "ma_cle" expirera après 120 secondes. Pour savoir la durée de vie restante d'une clé, on utilise `TTL` :

```bash
TTL ma_cle
```

## Structures de Données dans Redis
Redis offre plusieurs structures de données différentes :

### 1. Les Listes
- On peut insérer des éléments avec `LPUSH` (insertion à gauche) et `RPUSH` (insertion à droite).
- Pour lire les éléments d'une liste, on utilise `LRANGE`, en précisant l'indice de début et de fin :

  ```bash
  LRANGE ma_liste 0 -1
  ```

### 2. Les Ensembles (Sets)
- Les ensembles sont comme des listes, mais **les éléments sont distincts**.
- On peut ajouter un élément avec `SADD`, et afficher les éléments avec `SMEMBERS` :

  ```bash
  SADD mon_ensemble "element1"
  SMEMBERS mon_ensemble
  ```

- Pour supprimer un élément, on utilise `SREM`.

 - Pour réaliser l'**union de deux ensembles**, on utilise la commande `SUNION` :

  ```bash
  SUNION ensemble1 ensemble2
  ```

Cette commande renvoie un ensemble contenant tous les éléments uniques des deux ensembles.


# Compte-rendu-TP-BDD-NoSQL---INFO3 (Partie 2)

## Sets Ordonnés (Sorted Sets)
Les **sets ordonnés** sont une autre structure de données qui permettent de **classer des éléments avec un score associé**, on les utilise souvent dans le cadre de systèmes de recommandation, pour classer des utilisateurs par score.

Pour définir un set ordonné, on utilise la commande `ZADD` (au lieu de `SADD` pour les sets non ordonnés) :

```bash
ZADD scores 19 "Augustin"
ZADD scores 18 "Inès"
ZADD scores 8 "Samir"
```

Ici, chaque utilisateur est associé à un score. Pour **récupérer les éléments**, on utilise `ZRANGE`, de la même manière que `LRANGE` pour les listes :

```bash
ZRANGE scores 0 -1
```

Cette commande renvoie les éléments **par ordre croissant de score**. Pour les récupérer en ordre décroissant, on utilise `ZREVRANGE` :

```bash
ZREVRANGE scores 0 -1
```

Si l'on souhaite **connaître la position d'un élément** dans le classement, on utilise `ZRANK` :

```bash
ZRANK scores "Augustin"
```

ça renvoie l'indice de "Augustin" dans le set, en ordre croissant de score.

Les sets ordonnés sont utiles pour **classer des éléments** et effectuer des recherches basées sur des scores, souvent en tant que cache pour des bases de données relationnelles, permettant ainsi d'accélérer les accès aux données.

## Les Hashes
Les **hashes** sont une autre structure de données comparables à des **objets ou des dictionnaires** en programmation : une clé n'a pas seulement une valeur, mais une collection de paires champ-valeur.

Pour définir un hash, on utilise la commande `HSET` :

```bash
HSET user:211 username "Yusuf" age 31 email "yusuf@example.com"
```

Pour **récupérer toutes les informations** du hash, on utilise `HGETALL` :

```bash
HGETALL user:211
```

Cela renvoie toutes les paires champ-valeur du hash spécifié.

Il est également possible de **définir un hash en une seule commande** avec `HMSET` :

```bash
HMSET user:404 username "Augustin" age 25 email "augustin@gmail.com"
```

Pour **incrémenter un champ** dans un hash, comme l'âge, on utilise `HINCRBY` :

```bash
HINCRBY user:404 age 4
```

Cela incrémente l'âge de 4, passant de 25 à 29.

Les hashes sont très pratiques pour stocker des objets structurés sans schéma fixe, et permettent de regrouper plusieurs informations sous une seule clé.


# Compte-rendu-TP-BDD-NoSQL---INFO3 (Partie 3)

## Publication/Souscription (Pub/Sub)
Les **pub/sub** sont une structure de données bien adaptée aux **applications temps réel** comme les systèmes de messagerie, notifications, ou inscriptions sur différents canaux de communication.

Pour tester la fonctionnalité pub/sub, nous devons avoir plusieurs clients connectés au même serveur Redis.

1. Pour commencer, on démarre le serveur Redis :

    ```bash
    redis-server
    ```

2. Ensuite, on se connecte avec un premier client :

    ```bash
    redis-cli
    ```

    Le client **souscrit** à un canal, par exemple "mes cours" :

    ```bash
    SUBSCRIBE mes_cours
    ```

    Le client reste à l'écoute des messages qui seront envoyés sur ce canal.

3. Avec un deuxième client connecté au même serveur Redis, on peut **publier un message** sur le canal "mes cours" :

    ```bash
    PUBLISH mes_cours "Nouveau cours disponible sur la plateforme"
    ```

    Le premier client reçoit instantanément ce message, de manière **temps réel**. Tous les clients inscrits au canal "mes cours" recevront ce message.

On peut aussi **souscrire à plusieurs canaux** en utilisant des motifs (patterns) avec `PSUBSCRIBE`. Par exemple, pour s'inscrire à tous les canaux commençant par "mes" :

```bash
PSUBSCRIBE mes*
```

Ainsi, ce client recevra tous les messages des canaux correspondant au motif "mes*".

## Gestion des Clés et Bases de Données
Redis permet aussi une gestion efficace des clés et bases de données.

- Pour **afficher toutes les clés** définies lors de la session, on utilise la commande `KEYS` :

    ```bash
    KEYS *
    ```

    Cela renvoie la liste de toutes les clés existantes dans la base de données courante.

- Par défaut, Redis offre **16 bases de données**, numérotées de 0 à 15. Pour **changer de base de données**, on utilise la commande `SELECT` :

    ```bash
    SELECT 1
    ```

    Cela permet de passer à la base de données numéro 1. Pour vérifier les clés présentes dans cette base de données :

    ```bash
    KEYS *
    ```

    Si aucune clé n'a été définie dans cette base, le résultat sera vide.

Pour revenir à la base de données par défaut (la base numéro 0) :

```bash
SELECT 0
```

Par défaut, Redis n'écrit pas immédiatement les données sur disque. Cela signifie qu'en cas de panne, **toutes les données récentes peuvent être perdues**. Il est essentiel de configurer correctement Redis si l'on souhaite garantir la persistance des données, en activant les options d'écriture sur disque.
