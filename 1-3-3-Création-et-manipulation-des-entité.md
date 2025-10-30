# 1-Persistance des données et bases de données en Go

## 3-Utilisation d'un ORM simple en Go

### 3-Création et manipulation des entités avec l'ORM

---

La création et manipulation des entités représentent l’essence même de l’usage d’un ORM (Object-Relational Mapping). En Go, avec GORM, cela consiste à définir des structs qui correspondent aux tables de la base, puis à utiliser des méthodes simples pour effectuer les opérations CRUD (Create, Read, Update, Delete) sur ces entités.

---

## 1. Définition des entités (modèles)

Une entité est représentée par une struct Go. Les tags GORM permettent de configurer les propriétés des colonnes, contraintes et relations.

### Exemple d’un modèle `User`

```go
type User struct {
    ID        uint      `gorm:"primaryKey"`
    Name      string    `gorm:"size:100;not null"`
    Email     string    `gorm:"unique;not null"`
    Age       int
    CreatedAt time.Time
    UpdatedAt time.Time
}
```

- `gorm:"primaryKey"` : indique la clé primaire.
- `size`, `not null`, `unique` : contraintes SQL.
- `CreatedAt`, `UpdatedAt` sont automatiquement gérées par GORM.

---

## 2. Création (INSERT)

L’insertion se fait via la méthode `Create`.

```go
user := User{Name: "John Doe", Email: "john.doe@example.com", Age: 30}
result := db.Create(&user)
if result.Error != nil {
    log.Fatalf("Erreur création utilisateur : %v", result.Error)
}
fmt.Printf("Nouvel utilisateur ID: %d\n", user.ID)
```

GORM remplit automatiquement les champs comme l’ID auto-incrémenté.

---

## 3. Lecture (SELECT)

La recherche peut se faire de multiples manières :

- **Premier enregistrement selon une condition :**

```go
var user User
db.First(&user, "email = ?", "john.doe@example.com")
```

- **Tous les utilisateurs avec une condition :**

```go
var users []User
db.Where("age > ?", 20).Find(&users)
```

---

## 4. Mise à jour (UPDATE)

On peut utiliser `db.Model().Updates()` ou modifier l'entité puis `Save`.

```go
var user User
db.First(&user, 1)
user.Age = 31
db.Save(&user)

// Ou en une ligne
db.Model(&user).Updates(User{Age: 31, Name: "John Updated"})
```

---

## 5. Suppression (DELETE)

GORM propose aussi la suppression :

```go
db.Delete(&User{}, 1) // supprime par ID

// Ou suppression par condition
db.Where("name = ?", "John Doe").Delete(&User{})
```

---

## 6. Gestion des relations entre entités

GORM supporte facilement les relations.

### Exemple relation `User` - `Orders` (Has Many)

```go
type User struct {
    ID     uint
    Name   string
    Orders []Order `gorm:"foreignKey:UserID"`
}

type Order struct {
    ID      uint
    Item    string
    UserID  uint
}
```

Pour précharger les commandes liées :

```go
var user User
db.Preload("Orders").First(&user, 1)
fmt.Println(user.Orders)
```

---

## Diagramme Mermaid : Cycle CRUD avec ORM GORM

```mermaid
flowchart TD
    A[Démarrer] --> B[Définir struct modèle]
    B --> C[Migration AutoMigrate]
    C --> D[CREATE: db.Create()]
    D --> E[READ: db.First() / db.Find()]
    E --> F[UPDATE: db.Save() ou db.Model().Updates()]
    F --> G[DELETE: db.Delete()]
    G --> H[Gestion des relations avec Preload()]
    H --> I[Fin]
```

---

## Points clés à retenir

- GORM mappe automatiquement structs↔tables.
- Les tags struct permettent de configurer les colonnes.
- Les méthodes `Create`, `First`, `Find`, `Save`, et `Delete` gèrent CRUD.
- Les relations sont déclarées dans les structs avec des tags spécifiques.
- Utiliser `Preload` pour charger les associations.

---

## Sources

- Documentation officielle GORM – CRUD : https://gorm.io/docs/crud.html
- Relations dans GORM : https://gorm.io/docs/associations.html
- Tutoriel DigitalOcean sur GORM : https://www.digitalocean.com/community/tutorials/how-to-use-gorm-to-access-a-database-with-golang
- Article Medium sur les relations GORM : https://medium.com/swlh/gorm-associations-in-golang-b0c250f1304e

---

La manipulation d’entités avec un ORM simple comme GORM offre une syntaxe expressive et intuitive pour travailler avec les données relationnelles en Go. Cette approche fluidifie le code et réduit la complexité liée à la gestion directe des requêtes SQL.