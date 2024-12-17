# CouchDB : Base de données NoSQL orientée documents

CouchDB est un système de gestion de bases de données **NoSQL** qui stocke les données sous forme de documents **JSON**. Il offre une interface REST accessible via HTTP, ce qui facilite son intégration dans des applications web.

---

## Démarrage

### Exécution via Docker
Pour lancer CouchDB dans un conteneur Docker, utilisez la commande suivante :

```bash
docker run -d --name couchdbdemo -e COUCHDB_USER=abdoulaye -e COUCHDB_PASSWORD=barry -p 5984:5984 couchdb
```

**Explications :**
- `-d` : Exécuter en mode détaché.
- `--name couchdbdemo` : Nommer le conteneur.
- `-e` : Définir des variables d’environnement (utilisateur et mot de passe).
- `-p 5984:5984` : Mapper le port **5984** du conteneur sur l’hôte.

Cela télécharge l’image **CouchDB** et lance le service.

**Remarque** : Si vous utilisez Docker, pensez à **mapper les volumes** pour éviter la perte de données en cas d’arrêt ou de suppression du conteneur.

### Vérification
Pour vérifier que le conteneur est en cours d’exécution, utilisez :

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
Renvoie `{"ok":true}` si la base est créée.

---

### 2. Lire les informations d’une base (GET)

```bash
curl -X GET http://abdoulaye:barry@localhost:5984/films
```

Retourne les caractéristiques de la base (nombre de documents, etc.).

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

**Exemple de structure du fichier :**
```json
{
  "docs": [
    { "_id": "movie:1", "titre": "Inception", "annee": 2010 },
    { "_id": "movie:2", "titre": "Matrix", "annee": 1999 }
  ]
}
```

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

## Remarques importantes

- **Flexibilité des données** : CouchDB permet d'insérer des documents JSON sans schéma préétabli, mais cela peut causer des redondances ou des incohérences si les données ne sont pas structurées correctement.
- **Versionnage** : Chaque document est versionné via le champ `_rev`, facilitant le suivi des modifications.
- **Environnement distribué** : CouchDB est adapté pour gérer des données dans un environnement distribué avec des mécanismes de réplication et de tolérance aux pannes.

---

## Conclusion

CouchDB est un système simple, flexible et adapté aux environnements distribués grâce à sa gestion de versionnage, sa tolérance aux pannes et son API REST intuitive. Toutefois, pour éviter les redondances ou incohérences, une bonne structuration des données reste essentielle, même dans un système sans schéma.
