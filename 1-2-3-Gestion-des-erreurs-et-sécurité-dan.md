# 1-Persistance des données et bases de données en Go

## 2-Utilisation de la bibliothèque SQL standard

### 3-Gestion des erreurs et sécurité dans les requêtes

---

L’interaction avec une base de données au moyen de la bibliothèque standard `database/sql` en Go exige une attention particulière à la gestion des erreurs et aux bonnes pratiques de sécurité. Cet article expose les méthodes pour capturer et traiter efficacement les erreurs, ainsi que les mécanismes essentiels pour protéger les requêtes SQL contre les vulnérabilités telles que l’injection SQL.

---

## Gestion des erreurs dans `database/sql`

### 1. Vérification systématique des erreurs

Presque toutes les fonctions retournent un `error`. Ignorer cette valeur peut conduire à des comportements inattendus ou à des failles silencieuses.

```go
result, err := db.Exec("UPDATE users SET email=$1 WHERE id=$2", newEmail, userID)
if err != nil {
    log.Fatalf("Erreur lors de la mise à jour : %v", err)
}
```

### 2. Différencier erreurs types

- **`sql.ErrNoRows`** : Lorsque la requête SELECT ne renvoie aucun résultat, `QueryRow().Scan()` renvoie cette erreur. On peut alors traiter ce cas de façon spécifique.

```go
err := db.QueryRow("SELECT name FROM users WHERE id=$1", userID).Scan(&name)
if err == sql.ErrNoRows {
    fmt.Println("Aucun utilisateur trouvé avec cet ID")
} else if err != nil {
    log.Fatalf("Erreur inconnue : %v", err)
}
```

- **Erreurs de connexion** : Contrôler l’accessibilité au serveur avec `db.Ping()`.

### 3. Gestion des erreurs de transaction

```go
tx, err := db.Begin()
if err != nil {
    return err
}
defer tx.Rollback() // rollback automatique si commit non appelé

_, err = tx.Exec("INSERT INTO accounts (name) VALUES ($1)", "Compte A")
if err != nil {
    return err
}

err = tx.Commit()
if err != nil {
    return err
}
```

---

## Sécurité dans les requêtes SQL

### 1. Importance des requêtes paramétrées (prepared statements)

Les requêtes SQL construites par concaténation ou interpolation directe ouvrent la porte à l'injection SQL, une faille gravissime.

```go
// A éviter : concaténation directe
query := fmt.Sprintf("SELECT * FROM users WHERE name = '%s'", username) 
```

Au lieu de cela, toujours utiliser les placeholders `$1, $2` et passer les paramètres séparément.

```go
err := db.QueryRow("SELECT * FROM users WHERE name = $1", username).Scan(&user.ID, &user.Name)
```

Cela garantit que les valeurs sont automatiquement échappées, empêchant la manipulation malveillante.

### 2. Utilisation des `Prepare` pour optimiser et sécuriser

Lorsqu’une requête est exécutée plusieurs fois, la préparer améliore les performances et renforce la sécurité.

```go
stmt, err := db.Prepare("INSERT INTO logs(message) VALUES ($1)")
if err != nil {
    log.Fatal(err)
}
defer stmt.Close()

for _, msg := range messages {
    _, err := stmt.Exec(msg)
    if err != nil {
        log.Println("Erreur insertion log:", err)
    }
}
```

### 3. Autres bonnes pratiques

- **Limiter les privilèges de l’utilisateur DB** : utiliser un compte DB ne pouvant faire que ce dont l’application a besoin.
- **Éviter de stocker des données sensibles en clair**.
- **S’assurer de la validation et sanitation des données côté application** même si les requêtes sont paramétrées.
- **Utiliser TLS/SSL dans la connexion à la base** pour chiffrer les échanges.

---

## Exemple complet de gestion combinée erreurs + sécurité

```go
func getUserByID(db *sql.DB, id int) (*User, error) {
    var user User
    err := db.QueryRow("SELECT id, name, email FROM users WHERE id=$1", id).Scan(&user.ID, &user.Name, &user.Email)
    if err != nil {
        if err == sql.ErrNoRows {
            return nil, fmt.Errorf("utilisateur %d introuvable", id)
        }
        return nil, err
    }
    return &user, nil
}
```

---

## Diagramme Mermaid : Gestion de requêtes sécurisées avec erreurs

```mermaid
flowchart TD
    A[Début requête] --> B[Préparer ou exécuter requête paramétrée]
    B --> C{Erreur retournée ?}
    C -->|Oui| D[Cas spécial ? (ex: sql.ErrNoRows)]
    C -->|Non| E[Continuer traitement des données]
    D -->|Oui| F[Gestion cas spécifique]
    D -->|Non| G[Log + remonter erreur]
    E --> H[Terminer avec succès]
    F --> H
    G --> H
```

---

## Sources et références

- Documentation officielle Go `database/sql` : https://pkg.go.dev/database/sql
- OWASP Injection SQL : https://owasp.org/www-community/attacks/SQL_Injection
- Tutoriel Postgres & Go – Paramétrage sécuritaire : https://www.calhoun.io/he-uses-postgresql-and-go-database-sql-part-1/
- FAQ Go sur la gestion des erreurs : https://go.dev/doc/effective_go#error_handling

---

Cet article insiste sur un schéma précis de contrôle des erreurs et d’écriture sécurisée des requêtes SQL en Go. Ces pratiques sont indispensables pour produire du code fiable et sûr, potentiellement en production.