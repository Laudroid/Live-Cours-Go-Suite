# 1-Persistance des données et bases de données en Go

## 2-Utilisation de la bibliothèque SQL standard

### 1-Connexion à une base de données relationnelle (ex : PostgreSQL)

---

Go fournit un package standard `database/sql` qui sert d’interface unifiée pour interagir avec différentes bases relationnelles, dont PostgreSQL. Ce package ne sait pas directement comment se connecter à ces bases, il nécessite un **pilote** spécifique — dans le cas de PostgreSQL, l’un des plus utilisés est `github.com/lib/pq`.

Cette architecture permet d’écrire du code générique indépendant du système de gestion de base utilisé.

---

## Étapes pour se connecter à PostgreSQL en Go

### 1. Installer le pilote PostgreSQL

Avec Go modules :

```bash
go get github.com/lib/pq
```

### 2. Importer les packages dans le code

```go
import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)
```

Le symbole `_` avant le chemin importe le pilote uniquement pour son initialiseur, indispensable pour que `database/sql` le reconnaisse.

---

### 3. Configurer la chaîne de connexion

PostgreSQL attend une chaîne de caractères (dsn) précisant les paramètres :

- utilisateur (`user`)
- mot de passe (`password`)
- nom de la base (`dbname`)
- hôte (`host`) et port (`port`)
- paramètres SSL (`sslmode`)

Exemple minimal :

```go
connStr := "user=postgres password=secret dbname=mydb sslmode=disable"
```

Pour plus de sécurité en production, évitez d’inclure le mot de passe en clair dans le code.

---

### 4. Ouvrir la connexion avec `sql.Open`

```go
db, err := sql.Open("postgres", connStr)
if err != nil {
    panic(err)
}
defer db.Close()
```

À noter que `sql.Open` ne crée pas immédiatement une connexion au serveur, mais prépare l'interface. La connexion sera établie lors de la première opération.

---

### 5. Vérifier la connexion avec `Ping()`

Pour s’assurer que la base est disponible, exécuter une commande Ping :

```go
if err := db.Ping(); err != nil {
    panic(err)
}
```

---

## Exemple complet

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

func main() {
    connStr := "user=postgres password=secret dbname=mydb sslmode=disable"
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        panic(err)
    }
    defer db.Close()

    if err = db.Ping(); err != nil {
        panic(err)
    }

    fmt.Println("Connexion réussie à la base PostgreSQL")
}
```

---

## Gestion des erreurs et bonnes pratiques

- Toujours fermer la connexion avec `defer db.Close()`.
- Utiliser `Ping()` pour tester la disponibilité.
- Gérer explicitement les erreurs.
- Préparer et paramétrer les requêtes pour éviter les injections SQL (`db.QueryRow`, `db.Prepare`).
- Utiliser des pools de connexions gérés automatiquement par `database/sql`.
- Pour les environnements complexes, privilégier l’usage de variables d’environnement ou fichiers de configuration pour les paramètres.

---

## Diagramme Mermaid : Processus de connexion à PostgreSQL avec Go

```mermaid
flowchart TD
    A[Début programme Go] --> B[Import packages database/sql + pilote]
    B --> C[Composer chaîne de connexion]
    C --> D[Appeler sql.Open()]
    D --> E[Exécuter db.Ping()]
    E -->|Succès| F[Connexion établie prête à être utilisée]
    E -->|Erreur| G[Erreur de connexion, gérer erreur]
    F --> H[Exécuter requêtes SQL]
    H --> I[Fin]
```

---

## Sources

- Documentation officielle Go `database/sql` : https://pkg.go.dev/database/sql
- Pilote PostgreSQL pour Go `lib/pq` : https://github.com/lib/pq
- Guide DigitalOcean "How To Use PostgreSQL With Go" : https://www.digitalocean.com/community/tutorials/how-to-use-postgresql-with-go
- PostgreSQL Connection strings documentation : https://www.postgresql.org/docs/current/libpq-connect.html

---

Cette méthode standardisée en Go facilite l’interfaçage avec PostgreSQL, et peut être étendue, avec peu de changement, à d’autres bases relationnelles via leurs pilotes respectifs.