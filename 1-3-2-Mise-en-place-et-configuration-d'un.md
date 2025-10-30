# 1-Persistance des données et bases de données en Go

## 3-Utilisation d'un ORM simple en Go

### 2-Mise en place et configuration d'un ORM

---

Les Object-Relational Mappers (ORM) en Go, comme GORM, permettent de manipuler une base de données relationnelle avec une couche d’abstraction orientée objet. Cette partie détaille la mise en place initiale, la configuration et les bonnes pratiques pour démarrer avec un ORM Go.

---

## 1. Installer l’ORM et le driver de base de données

Pour utiliser GORM avec PostgreSQL par exemple, il faut installer :

```bash
go get -u gorm.io/gorm
go get -u gorm.io/driver/postgres
```

---

## 2. Établir la connexion à la base

GORM utilise un DSN (Data Source Name) pour se connecter à la base. Exemple avec PostgreSQL :

```go
import (
    "gorm.io/driver/postgres"
    "gorm.io/gorm"
)

dsn := "host=localhost user=postgres password=secret dbname=mydb port=5432 sslmode=disable"
db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{})
if err != nil {
    panic("Échec de la connexion à la base")
}
```

Le type `*gorm.DB` est le handle principal pour interagir avec la base.

---

## 3. Configuration optionnelle

### Mode d’enregistrement des logs SQL

Permet de voir les requêtes SQL générées, utile en développement :

```go
db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{
    Logger: logger.Default.LogMode(logger.Info),
})
```

### Pool de connexions

GORM utilise sous-jacent `database/sql` et contrôle automatiquement le pool de connexions. Pour configurer explicitement :

```go
sqlDB, err := db.DB()
if err != nil {
    panic(err)
}
sqlDB.SetMaxOpenConns(100)  // Max connexions ouvertes
sqlDB.SetMaxIdleConns(10)   // Max connexions en attente
sqlDB.SetConnMaxLifetime(time.Hour)
```

---

## 4. Utilisation de modèles et migration automatique

Définir un struct Go avec « tags » GORM pour modéliser une table :

```go
type Product struct {
    ID          uint   `gorm:"primaryKey"`
    Code        string `gorm:"unique"`
    Price       uint
    CreatedAt   time.Time
    UpdatedAt   time.Time
}
```

Lancer la migration :

```go
err = db.AutoMigrate(&Product{})
if err != nil {
    panic(err)
}
```

Cela crée la table et les colonnes si elles n’existent pas.

---

## 5. Gestion des erreurs

Toutes les opérations retournent un résultat, souvent de type `*gorm.DB`, à vérifier :

```go
result := db.Create(&Product{Code: "L1212", Price: 1000})
if result.Error != nil {
    log.Fatalf("Erreur création produit: %v", result.Error)
}
```

---

## 6. Exemple complet minimal

```go
package main

import (
    "log"
    "time"
    "gorm.io/driver/postgres"
    "gorm.io/gorm"
    "gorm.io/gorm/logger"
)

type Product struct {
    ID        uint `gorm:"primaryKey"`
    Code      string
    Price     uint
    CreatedAt time.Time
    UpdatedAt time.Time
}

func main() {
    dsn := "host=localhost user=postgres password=secret dbname=mydb port=5432 sslmode=disable"
    db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{
        Logger: logger.Default.LogMode(logger.Info),
    })
    if err != nil {
        log.Fatal("Connexion à la base échouée:", err)
    }

    sqlDB, _ := db.DB()
    sqlDB.SetMaxOpenConns(50)
    sqlDB.SetMaxIdleConns(10)
    sqlDB.SetConnMaxLifetime(time.Hour)

    err = db.AutoMigrate(&Product{})
    if err != nil {
        log.Fatal("Migration échouée:", err)
    }

    product := Product{Code: "D42", Price: 200}
    if err := db.Create(&product).Error; err != nil {
        log.Fatal("Erreur création produit:", err)
    }
}
```

---

## Diagramme Mermaid : Mise en place et configuration d’un ORM en Go

```mermaid
flowchart TD
    A[Installer packages GORM et driver] --> B[Établir connexion avec gorm.Open]
    B --> C[Configurer logging et pool de connexions]
    C --> D[Définir modèles Go (structs)]
    D --> E[Lancer migration automatique AutoMigrate]
    E --> F[Utiliser les méthodes CRUD de GORM]
```

---

## Sources

- Documentation officielle GORM : https://gorm.io/docs/
- Tutoriel DigitalOcean : https://www.digitalocean.com/community/tutorials/how-to-use-gorm-to-access-a-database-with-golang
- Gestion configuration pools `database/sql` : https://golang.org/pkg/database/sql/#DB.SetMaxOpenConns
- Blog GORM (exemples) : https://gorm.io/docs/logger.html

---

La mise en place et la configuration d’un ORM avec Go s’appuient sur une séquence claire : installer, connecter, configurer, modéliser, migrer et manipuler. Ce flux permet un développement efficace en limitant les erreurs et favorisant la maintenance.