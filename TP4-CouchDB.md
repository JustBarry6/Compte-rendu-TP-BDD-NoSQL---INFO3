# CouchDB : Base de données NoSQL orientée documents

CouchDB est un système de gestion de bases de données **NoSQL** qui stocke les données sous forme de documents **JSON**. Il offre une interface REST accessible via HTTP, ce qui facilite son intégration dans des applications web.

---

## Démarrage

### Exécution via Docker
Pour lancer CouchDB dans un conteneur Docker, on utilise la commande suivante :

```bash
docker run -d --name couchdbdemo -e COUCHDB_USER=abdoulaye -e COUCHDB_PASSWORD=barry -p 5984:5984 couchdb
```

**Explications :**
- `-d` : On exécute en mode détaché.
- `--name couchdbdemo` : On nomme le conteneur.
- `-e` : On définit des variables d’environnement (utilisateur et mot de passe).
- `-p 5984:5984` : On mappe le port **5984** du conteneur sur l’hôte.

Cela télécharge l’image **CouchDB** et lance le service.

**Remarque** : Si on utilise Docker, il faut penser à **mapper les volumes** pour éviter la perte de données en cas d’arrêt ou de suppression du conteneur.

### Vérification
Pour vérifier que le conteneur est en cours d’exécution, on utilise :

```bash
docker ps
```

### Interface graphique
CouchDB propose une interface web disponible à l’adresse suivante :

```
http://localhost:5984/_utils
```

Elle permet de gérer les bases et consulter les documents. Une documentation locale intégrée est également disponible.

---

## Commandes CRUD via REST

CouchDB utilise les verbes HTTP (**GET**, **PUT**, **POST**, **DELETE**). On peut interagir avec CouchDB à l’aide de l’outil **curl**.

### 1. Créer une base de données (PUT)

```bash
curl -X PUT http://abdoulaye:barry@localhost:5984/films
```

**Réponse** :  
On renvoie `{"ok":true}` si la base est créée.

---

### 2. Lire les informations d’une base (GET)

```bash
curl -X GET http://abdoulaye:barry@localhost:5984/films
```

On retourne les caractéristiques de la base (nombre de documents, etc.).

---

### 3. Insertion de documents

- **Créer un document avec un ID connu (PUT)** :

```bash
curl -X PUT http://abdoulaye:barry@localhost:5984/films/doc1 \
-d '{"titre":"Inception"}' \
-H "Content-Type: application/json"
```

- **Créer un document sans ID (POST)** : CouchDB génère un ID automatiquement.

```bash
curl -X POST http://abdoulaye:barry@localhost:5984/films \
-d '{"titre":"Matrix"}' \
-H "Content-Type: application/json"
```

**Remarque** : Chaque document possède un champ `_id` unique et un champ `_rev` permettant de suivre les versions des modifications.

---

### 4. Insertion massive de documents

Pour insérer une collection de documents à partir d’un fichier `films_couchdb.json` :

**Commande :**
```bash
curl -X POST http://abdoulaye:barry@localhost:5984/films/_bulk_docs \
-d @films_couchdb.json \
-H "Content-Type: application/json"
```

---

### 5. Récupérer un document par ID (GET)

```bash
curl -X GET http://abdoulaye:barry@localhost:5984/films/movie:10098
```

---

### 6. Supprimer un document (DELETE)

```bash
curl -X DELETE http://abdoulaye:barry@localhost:5984/films/doc1
```

---
