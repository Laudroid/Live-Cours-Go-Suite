Bonjour à toutes et à tous,

Ce TP a pour objectif de vous familiariser avec deux approches fondamentales pour interagir avec une base de données en Go : l'utilisation de la bibliothèque standard `database/sql` et l'intégration d'un ORM (Object-Relational Mapper) populaire comme GORM.

---

### **TP : Gestion de Livres - `database/sql` vs. GORM**

**Objectif du TP :**
*   Maîtriser la connexion à une base de données SQLite en Go.
*   Implémenter des opérations CRUD (Create, Read, Update, Delete) en utilisant la bibliothèque standard `database/sql`.
*   Implémenter les mêmes opérations CRUD en utilisant l'ORM GORM.
*   Comprendre les avantages et inconvénients de chaque approche.

**Contexte du mini-projet :**
Vous allez développer une petite application de gestion de livres. Chaque livre aura au minimum un titre, un auteur, une année de publication et un ISBN.

**Prérequis :**
*   Go installé sur votre machine (version 1.18 ou supérieure).
*   Connaissances de base en Go (structures, fonctions, gestion des erreurs).
*   Un éditeur de code (VS Code, GoLand, etc.).

**Mise en place du projet :**

1.  Créez un nouveau répertoire pour votre projet :
    ```bash
    mkdir go-db-tp
    cd go-db-tp
    go mod init go-db-tp
    ```

2.  Installez les dépendances nécessaires :
    *   Pour SQLite :
        ```bash
        go get github.com/mattn/go-sqlite3
        ```
    *   Pour GORM et son driver SQLite :
        ```bash
        go get gorm.io/gorm
        go get gorm.io/driver/sqlite
        ```

---

### **Partie 1 : Utilisation de `database/sql` (Bibliothèque Standard)**

Dans cette partie, vous allez interagir directement avec la base de données en écrivant des requêtes SQL brutes.

1.  **Définition du modèle `Book` :**
    Créez une structure Go `Book` qui représentera un livre. Elle devra contenir les champs suivants :
    *   `ID` (int) : Identifiant unique du livre.
    *   `Title` (string) : Titre du livre.
    *   `Author` (string) : Auteur du livre.
    *   `PublishedYear` (int) : Année de publication.
    *   `ISBN` (string) : Numéro ISBN unique.

2.  **Connexion à la base de données :**
    *   Établissez une connexion à une base de données SQLite nommée `books_standard.db`.
    *   Assurez-vous de fermer la connexion proprement avec `defer db.Close()`.

3.  **Création de la table `books` :**
    *   Exécutez une requête SQL `CREATE TABLE IF NOT EXISTS` pour créer la table `books` avec les colonnes correspondant à votre structure `Book`.
    *   `ID` doit être une clé primaire auto-incrémentée.

4.  **Implémentation des opérations CRUD :**
    Créez des fonctions distinctes pour chaque opération :

    *   **`CreateBook(book Book) (int64, error)` :**
        *   Insère un nouveau livre dans la table.
        *   Retourne l'ID du livre inséré et une erreur éventuelle.
        *   Utilisez des requêtes préparées (`db.Prepare` ou `db.Exec` avec des placeholders `?`).

    *   **`GetBookByID(id int) (*Book, error)` :**
        *   Récupère un livre par son ID.
        *   Retourne un pointeur vers un `Book` et une erreur.
        *   Utilisez `db.QueryRow` et `row.Scan()`.

    *   **`GetAllBooks() ([]Book, error)` :**
        *   Récupère tous les livres de la table.
        *   Retourne un slice de `Book` et une erreur.
        *   Utilisez `db.Query`, puis itérez sur `rows.Next()` et `rows.Scan()`.

    *   **`UpdateBook(book Book) error` :**
        *   Met à jour un livre existant (par son ID).
        *   Retourne une erreur si la mise à jour échoue.

    *   **`DeleteBook(id int) error` :**
        *   Supprime un livre par son ID.
        *   Retourne une erreur si la suppression échoue.

5.  **Fonction `main` de test :**
    *   Dans votre fonction `main`, appelez ces fonctions pour :
        *   Créer quelques livres.
        *   Afficher tous les livres.
        *   Récupérer un livre par ID et l'afficher.
        *   Mettre à jour un livre et réafficher tous les livres.
        *   Supprimer un livre et réafficher tous les livres.
    *   Gérez les erreurs de manière simple (par exemple, `log.Fatal` pour les erreurs critiques).

---

### **Partie 2 : Utilisation de GORM (ORM)**

Dans cette partie, vous allez réimplémenter les mêmes opérations CRUD en utilisant l'ORM GORM, ce qui abstraira une grande partie de l'écriture SQL.

1.  **Définition du modèle `Book` pour GORM :**
    *   Réutilisez votre structure `Book` de la Partie 1.
    *   Pour que GORM puisse gérer l'ID et les timestamps (`CreatedAt`, `UpdatedAt`, `DeletedAt`), vous pouvez embarquer `gorm.Model` dans votre structure :
        ```go
        type Book struct {
            gorm.Model // Ajoute ID, CreatedAt, UpdatedAt, DeletedAt
            Title        string
            Author       string
            PublishedYear int
            ISBN         string `gorm:"unique"` // Exemple de tag GORM
        }
        ```
    *   Ajoutez un tag `gorm:"unique"` sur le champ `ISBN` pour garantir son unicité.

2.  **Connexion à la base de données :**
    *   Établissez une connexion à une base de données SQLite nommée `books_gorm.db` en utilisant l'API de GORM.
    *   Assurez-vous de fermer la connexion.

3.  **Migration automatique de la table :**
    *   Utilisez `db.AutoMigrate(&Book{})` pour que GORM crée ou mette à jour la table `books` en fonction de votre structure `Book`.

4.  **Implémentation des opérations CRUD avec GORM :**
    Créez des fonctions distinctes pour chaque opération, en utilisant les méthodes de GORM :

    *   **`CreateBookGORM(book *Book) error` :**
        *   Insère un nouveau livre. GORM mettra à jour l'ID de l'objet `book` passé en paramètre.
        *   Utilisez `db.Create(book)`.

    *   **`GetBookByIDGORM(id uint) (*Book, error)` :**
        *   Récupère un livre par son ID.
        *   Utilisez `db.First(&book, id)`.

    *   **`GetAllBooksGORM() ([]Book, error)` :**
        *   Récupère tous les livres.
        *   Utilisez `db.Find(&books)`.

    *   **`UpdateBookGORM(book *Book) error` :**
        *   Met à jour un livre existant.
        *   Utilisez `db.Save(book)`.

    *   **`DeleteBookGORM(id uint) error` :**
        *   Supprime un livre par son ID.
        *   Utilisez `db.Delete(&Book{}, id)`.

5.  **Fonction `main` de test :**
    *   Dans votre fonction `main` (ou une fonction de test séparée), appelez ces fonctions GORM pour effectuer les mêmes opérations de test que dans la Partie 1.
    *   Gérez les erreurs retournées par GORM (par exemple, `db.Error`).

---

### **Partie 3 : Comparaison et Réflexion**

Cette partie est cruciale pour consolider votre apprentissage. Répondez aux questions suivantes, en vous basant sur votre expérience des deux parties.

1.  **Code et lisibilité :**
    *   Comparez la quantité de code nécessaire pour les opérations CRUD basiques entre `database/sql` et GORM.
    *   Quelle approche trouvez-vous la plus lisible pour les opérations simples ? Pourquoi ?

2.  **Gestion des erreurs :**
    *   Décrivez les différences dans la manière dont les erreurs sont gérées par `database/sql` et GORM.
    *   Quelle approche vous semble la plus robuste ou la plus facile à utiliser pour la gestion des erreurs ?

3.  **Flexibilité et contrôle :**
    *   Quand choisiriez-vous `database/sql` plutôt qu'un ORM comme GORM ? Donnez des exemples de scénarios.
    *   Quand un ORM serait-il plus avantageux ?

4.  **Sécurité :**
    *   Comment `database/sql` et GORM aident-ils à prévenir les injections SQL ?
    *   Y a-t-il des pièges à éviter avec l'une ou l'autre approche en termes de sécurité ?

5.  **Performance (brève discussion) :**
    *   Sans faire de benchmarks précis, quelles sont vos intuitions sur les performances relatives des deux approches pour des opérations simples ?
    *   Quels facteurs pourraient influencer la performance d'un ORM par rapport à des requêtes SQL directes ?

---

**Livraison :**

*   Votre code Go pour les deux parties (fichiers `.go`).
*   Un fichier texte ou Markdown (`REFLEXION.md`) contenant vos réponses aux questions de la Partie 3.

---

Bon courage pour ce TP ! N'hésitez pas à expérimenter et à poser des questions si vous rencontrez des difficultés.