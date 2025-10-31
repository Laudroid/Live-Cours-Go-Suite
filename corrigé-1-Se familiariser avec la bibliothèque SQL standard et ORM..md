Voici deux solutions complètes pour le TP de gestion de livres, utilisant respectivement la bibliothèque standard `database/sql` et l'ORM GORM. Chaque solution est commentée pour faciliter la compréhension.

---

# Solutions TP : Gestion de Livres - `database/sql` vs. GORM

## Partie 1 : Solution avec `database/sql`

Cette section présente une implémentation des opérations CRUD en utilisant la bibliothèque standard `database/sql` de Go, en interagissant directement avec des requêtes SQL.


```go
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/mattn/go-sqlite3" // Importation du driver SQLite
)

// Book représente la structure d'un livre pour database/sql.
type Book struct {
	ID            int
	Title         string
	Author        string
	PublishedYear int
	ISBN          string
}

// connectDBStandard établit une connexion à la base de données SQLite.
func connectDBStandard(dbPath string) (*sql.DB, error) {
	db, err := sql.Open("sqlite3", dbPath)
	if err != nil {
		return nil, fmt.Errorf("erreur lors de l'ouverture de la base de données : %w", err)
	}
	// Vérifie que la connexion est fonctionnelle.
	if err = db.Ping(); err != nil {
		db.Close() // Ferme la connexion en cas d'échec du ping.
		return nil, fmt.Errorf("erreur lors de la connexion à la base de données : %w", err)
	}
	return db, nil
}

// createTableStandard crée la table 'books' si elle n'existe pas.
func createTableStandard(db *sql.DB) error {
	createTableSQL := `
	CREATE TABLE IF NOT EXISTS books (
		id INTEGER PRIMARY KEY AUTOINCREMENT,
		title TEXT NOT NULL,
		author TEXT NOT NULL,
		published_year INTEGER NOT NULL,
		isbn TEXT UNIQUE NOT NULL
	);`
	_, err := db.Exec(createTableSQL)
	if err != nil {
		return fmt.Errorf("erreur lors de la création de la table : %w", err)
	}
	return nil
}

// CreateBook insère un nouveau livre dans la base de données.
// Utilise une requête préparée pour prévenir les injections SQL.
func CreateBook(db *sql.DB, book Book) (int64, error) {
	stmt, err := db.Prepare("INSERT INTO books(title, author, published_year, isbn) VALUES(?, ?, ?, ?)")
	if err != nil {
		return 0, fmt.Errorf("erreur lors de la préparation de la requête d'insertion : %w", err)
	}
	defer stmt.Close() // Assure la fermeture du statement.

	result, err := stmt.Exec(book.Title, book.Author, book.PublishedYear, book.ISBN)
	if err != nil {
		return 0, fmt.Errorf("erreur lors de l'exécution de l'insertion : %w", err)
	}

	id, err := result.LastInsertId()
	if err != nil {
		return 0, fmt.Errorf("erreur lors de la récupération de l'ID inséré : %w", err)
	}
	return id, nil
}

// GetBookByID récupère un livre par son ID.
func GetBookByID(db *sql.DB, id int) (*Book, error) {
	row := db.QueryRow("SELECT id, title, author, published_year, isbn FROM books WHERE id = ?", id)

	book := &Book{}
	err := row.Scan(&book.ID, &book.Title, &book.Author, &book.PublishedYear, &book.ISBN)
	if err == sql.ErrNoRows {
		return nil, nil // Livre non trouvé.
	}
	if err != nil {
		return nil, fmt.Errorf("erreur lors de la lecture du livre par ID : %w", err)
	}
	return book, nil
}

// GetAllBooks récupère tous les livres de la base de données.
func GetAllBooks(db *sql.DB) ([]Book, error) {
	rows, err := db.Query("SELECT id, title, author, published_year, isbn FROM books")
	if err != nil {
		return nil, fmt.Errorf("erreur lors de la récupération de tous les livres : %w", err)
	}
	defer rows.Close() // Assure la fermeture des lignes.

	var books []Book
	for rows.Next() {
		var book Book
		if err := rows.Scan(&book.ID, &book.Title, &book.Author, &book.PublishedYear, &book.ISBN); err != nil {
			return nil, fmt.Errorf("erreur lors du scan d'un livre : %w", err)
		}
		books = append(books, book)
	}
	// Vérifie les erreurs survenues pendant l'itération.
	if err = rows.Err(); err != nil {
		return nil, fmt.Errorf("erreur après l'itération des lignes : %w", err)
	}
	return books, nil
}

// UpdateBook met à jour un livre existant.
// Utilise une requête préparée.
func UpdateBook(db *sql.DB, book Book) error {
	stmt, err := db.Prepare("UPDATE books SET title = ?, author = ?, published_year = ?, isbn = ? WHERE id = ?")
	if err != nil {
		return fmt.Errorf("erreur lors de la préparation de la requête de mise à jour : %w", err)
	}
	defer stmt.Close()

	result, err := stmt.Exec(book.Title, book.Author, book.PublishedYear, book.ISBN, book.ID)
	if err != nil {
		return fmt.Errorf("erreur lors de l'exécution de la mise à jour : %w", err)
	}

	rowsAffected, err := result.RowsAffected()
	if err != nil {
		return fmt.Errorf("erreur lors de la vérification des lignes affectées : %w", err)
	}
	if rowsAffected == 0 {
		return fmt.Errorf("aucun livre trouvé avec l'ID %d pour la mise à jour", book.ID)
	}
	return nil
}

// DeleteBook supprime un livre par son ID.
// Utilise une requête préparée.
func DeleteBook(db *sql.DB, id int) error {
	stmt, err := db.Prepare("DELETE FROM books WHERE id = ?")
	if err != nil {
		return fmt.Errorf("erreur lors de la préparation de la requête de suppression : %w", err)
	}
	defer stmt.Close()

	result, err := stmt.Exec(id)
	if err != nil {
		return fmt.Errorf("erreur lors de l'exécution de la suppression : %w", err)
	}

	rowsAffected, err := result.RowsAffected()
	if err != nil {
		return fmt.Errorf("erreur lors de la vérification des lignes affectées : %w", err)
	}
	if rowsAffected == 0 {
		return fmt.Errorf("aucun livre trouvé avec l'ID %d pour la suppression", id)
	}
	return nil
}

func main() {
	dbPath := "./books_standard.db"
	db, err := connectDBStandard(dbPath)
	if err != nil {
		log.Fatalf("Échec de la connexion à la base de données : %v", err)
	}
	defer db.Close() // Assure la fermeture de la connexion à la fin du main.

	if err := createTableStandard(db); err != nil {
		log.Fatalf("Échec de la création de la table : %v", err)
	}
	fmt.Println("Table 'books' prête.")

	// --- Création de livres ---
	fmt.Println("\n--- Création de livres ---")
	book1 := Book{Title: "Le Seigneur des Anneaux", Author: "J.R.R. Tolkien", PublishedYear: 1954, ISBN: "978-2070612345"}
	id1, err := CreateBook(db, book1)
	if err != nil {
		log.Printf("Erreur lors de la création du livre 1 : %v", err)
	} else {
		fmt.Printf("Livre créé avec ID : %d\n", id1)
	}

	book2 := Book{Title: "Fondation", Author: "Isaac Asimov", PublishedYear: 1951, ISBN: "978-2070415700"}
	id2, err := CreateBook(db, book2)
	if err != nil {
		log.Printf("Erreur lors de la création du livre 2 : %v", err)
	} else {
		fmt.Printf("Livre créé avec ID : %d\n", id2)
	}

	// --- Affichage de tous les livres ---
	fmt.Println("\n--- Tous les livres ---")
	books, err := GetAllBooks(db)
	if err != nil {
		log.Fatalf("Erreur lors de la récupération de tous les livres : %v", err)
	}
	for _, b := range books {
		fmt.Printf("ID: %d, Titre: %s, Auteur: %s, Année: %d, ISBN: %s\n", b.ID, b.Title, b.Author, b.PublishedYear, b.ISBN)
	}

	// --- Récupération d'un livre par ID ---
	fmt.Println("\n--- Récupération d'un livre par ID (ID 1) ---")
	retrievedBook, err := GetBookByID(db, int(id1))
	if err != nil {
		log.Printf("Erreur lors de la récupération du livre par ID : %v", err)
	} else if retrievedBook != nil {
		fmt.Printf("Livre récupéré : ID: %d, Titre: %s\n", retrievedBook.ID, retrievedBook.Title)
	} else {
		fmt.Println("Livre non trouvé.")
	}

	// --- Mise à jour d'un livre ---
	fmt.Println("\n--- Mise à jour d'un livre (ID 1) ---")
	if retrievedBook != nil {
		retrievedBook.PublishedYear = 1965 // Nouvelle année de publication
		err = UpdateBook(db, *retrievedBook)
		if err != nil {
			log.Printf("Erreur lors de la mise à jour du livre : %v", err)
		} else {
			fmt.Printf("Livre ID %d mis à jour.\n", retrievedBook.ID)
		}
	}

	// --- Affichage de tous les livres après mise à jour ---
	fmt.Println("\n--- Tous les livres après mise à jour ---")
	books, err = GetAllBooks(db)
	if err != nil {
		log.Fatalf("Erreur lors de la récupération de tous les livres : %v", err)
	}
	for _, b := range books {
		fmt.Printf("ID: %d, Titre: %s, Auteur: %s, Année: %d, ISBN: %s\n", b.ID, b.Title, b.Author, b.PublishedYear, b.ISBN)
	}

	// --- Suppression d'un livre ---
	fmt.Println("\n--- Suppression d'un livre (ID 2) ---")
	err = DeleteBook(db, int(id2))
	if err != nil {
		log.Printf("Erreur lors de la suppression du livre : %v", err)
	} else {
		fmt.Printf("Livre ID %d supprimé.\n", id2)
	}

	// --- Affichage de tous les livres après suppression ---
	fmt.Println("\n--- Tous les livres après suppression ---")
	books, err = GetAllBooks(db)
	if err != nil {
		log.Fatalf("Erreur lors de la récupération de tous les livres : %v", err)
	}
	if len(books) == 0 {
		fmt.Println("Aucun livre restant.")
	}
	for _, b := range books {
		fmt.Printf("ID: %d, Titre: %s, Auteur: %s, Année: %d, ISBN: %s\n", b.ID, b.Title, b.Author, b.PublishedYear, b.ISBN)
	}
}
```


**Explications clés pour `database/sql` :**

*   **`sql.Open` et `db.Ping` :** `sql.Open` ne teste pas la connexion immédiatement. `db.Ping` est utilisé pour s'assurer que la base de données est accessible.
*   **`defer db.Close()` :** Indispensable pour libérer les ressources de la base de données une fois que la fonction `main` (ou toute fonction ouvrant une connexion) a terminé son exécution.
*   **Requêtes préparées (`db.Prepare`) :** Cruciales pour la sécurité. Elles permettent de séparer la requête SQL des données, prévenant ainsi les injections SQL. Les placeholders (`?` pour SQLite) sont remplacés par les valeurs fournies lors de `stmt.Exec`.
*   **`db.Exec` vs `db.Query` vs `db.QueryRow` :**
    *   `db.Exec` est utilisé pour les opérations qui ne retournent pas de lignes (INSERT, UPDATE, DELETE, CREATE TABLE). Il retourne un `sql.Result` qui peut donner l'ID inséré ou le nombre de lignes affectées.
    *   `db.Query` est pour les requêtes qui retournent plusieurs lignes (SELECT). Il retourne un `*sql.Rows` que l'on doit itérer avec `rows.Next()` et scanner avec `rows.Scan()`. Il est vital de `defer rows.Close()`.
    *   `db.QueryRow` est pour les requêtes qui ne devraient retourner qu'une seule ligne. Il retourne un `*sql.Row` sur lequel on appelle directement `Scan()`.
*   **Gestion des erreurs :** Chaque opération retourne une erreur. Il est important de vérifier ces erreurs et de les propager ou de les gérer (`log.Fatal` pour les erreurs irrécupérables dans `main`). `sql.ErrNoRows` est une erreur spécifique indiquant qu'aucune ligne n'a été trouvée.

## Partie 2 : Solution avec GORM

Cette section réimplémente les mêmes opérations CRUD en utilisant l'ORM GORM, qui abstrait une grande partie de l'écriture SQL et simplifie l'interaction avec la base de données.


```go
package main

import (
	"fmt"
	"log"

	"gorm.io/driver/sqlite"
	"gorm.io/gorm"
)

// Book représente la structure d'un livre pour GORM.
// gorm.Model inclut les champs ID, CreatedAt, UpdatedAt, DeletedAt.
// Le tag `gorm:"unique"` assure l'unicité de l'ISBN.
type Book struct {
	gorm.Model
	Title         string
	Author        string
	PublishedYear int
	ISBN          string `gorm:"unique"`
}

// connectDBGORM établit une connexion à la base de données SQLite avec GORM.
func connectDBGORM(dbPath string) (*gorm.DB, error) {
	db, err := gorm.Open(sqlite.Open(dbPath), &gorm.Config{})
	if err != nil {
		return nil, fmt.Errorf("échec de la connexion à la base de données GORM : %w", err)
	}
	return db, nil
}

// AutoMigrateTable utilise GORM pour créer ou mettre à jour la table 'books'.
func AutoMigrateTable(db *gorm.DB) error {
	// AutoMigrate va créer la table si elle n'existe pas,
	// ou ajouter des colonnes si elles manquent.
	err := db.AutoMigrate(&Book{})
	if err != nil {
		return fmt.Errorf("échec de l'auto-migration de la table : %w", err)
	}
	return nil
}

// CreateBookGORM insère un nouveau livre.
// GORM mettra à jour l'objet book avec l'ID généré.
func CreateBookGORM(db *gorm.DB, book *Book) error {
	result := db.Create(book)
	if result.Error != nil {
		return fmt.Errorf("erreur lors de la création du livre : %w", result.Error)
	}
	return nil
}

// GetBookByIDGORM récupère un livre par son ID.
func GetBookByIDGORM(db *gorm.DB, id uint) (*Book, error) {
	var book Book
	// First cherche la première entrée correspondant à la condition.
	// Si non trouvé, result.Error sera gorm.ErrRecordNotFound.
	result := db.First(&book, id)
	if result.Error == gorm.ErrRecordNotFound {
		return nil, nil // Livre non trouvé.
	}
	if result.Error != nil {
		return nil, fmt.Errorf("erreur lors de la récupération du livre par ID : %w", result.Error)
	}
	return &book, nil
}

// GetAllBooksGORM récupère tous les livres.
func GetAllBooksGORM(db *gorm.DB) ([]Book, error) {
	var books []Book
	result := db.Find(&books)
	if result.Error != nil {
		return nil, fmt.Errorf("erreur lors de la récupération de tous les livres : %w", result.Error)
	}
	return books, nil
}

// UpdateBookGORM met à jour un livre existant.
// GORM utilise l'ID de l'objet `book` pour savoir quelle entrée mettre à jour.
func UpdateBookGORM(db *gorm.DB, book *Book) error {
	result := db.Save(book) // Save met à jour si l'ID existe, insère sinon.
	if result.Error != nil {
		return fmt.Errorf("erreur lors de la mise à jour du livre : %w", result.Error)
	}
	if result.RowsAffected == 0 {
		return fmt.Errorf("aucun livre trouvé avec l'ID %d pour la mise à jour", book.ID)
	}
	return nil
}

// DeleteBookGORM supprime un livre par son ID.
// db.Delete(&Book{}, id) supprime l'entrée avec l'ID spécifié.
func DeleteBookGORM(db *gorm.DB, id uint) error {
	result := db.Delete(&Book{}, id)
	if result.Error != nil {
		return fmt.Errorf("erreur lors de la suppression du livre : %w", result.Error)
	}
	if result.RowsAffected == 0 {
		return fmt.Errorf("aucun livre trouvé avec l'ID %d pour la suppression", id)
	}
	return nil
}

func main() {
	dbPath := "./books_gorm.db"
	db, err := connectDBGORM(dbPath)
	if err != nil {
		log.Fatalf("Échec de la connexion à la base de données GORM : %v", err)
	}

	// GORM ne nécessite pas de fermeture explicite de la connexion pour SQLite
	// car la connexion est gérée en interne et fermée à la fin du processus.
	// Pour d'autres bases de données, db.Close() serait nécessaire.

	if err := AutoMigrateTable(db); err != nil {
		log.Fatalf("Échec de l'auto-migration de la table GORM : %v", err)
	}
	fmt.Println("Table 'books' GORM prête.")

	// --- Création de livres ---
	fmt.Println("\n--- Création de livres GORM ---")
	book1 := &Book{Title: "Le Hobbit", Author: "J.R.R. Tolkien", PublishedYear: 1937, ISBN: "978-2070612352"}
	err = CreateBookGORM(db, book1)
	if err != nil {
		log.Printf("Erreur lors de la création du livre 1 : %v", err)
	} else {
		fmt.Printf("Livre créé avec ID : %d\n", book1.ID)
	}

	book2 := &Book{Title: "Dune", Author: "Frank Herbert", PublishedYear: 1965, ISBN: "978-2226012357"}
	err = CreateBookGORM(db, book2)
	if err != nil {
		log.Printf("Erreur lors de la création du livre 2 : %v", err)
	} else {
		fmt.Printf("Livre créé avec ID : %d\n", book2.ID)
	}

	// --- Affichage de tous les livres ---
	fmt.Println("\n--- Tous les livres GORM ---")
	books, err := GetAllBooksGORM(db)
	if err != nil {
		log.Fatalf("Erreur lors de la récupération de tous les livres GORM : %v", err)
	}
	for _, b := range books {
		fmt.Printf("ID: %d, Titre: %s, Auteur: %s, Année: %d, ISBN: %s\n", b.ID, b.Title, b.Author, b.PublishedYear, b.ISBN)
	}

	// --- Récupération d'un livre par ID ---
	fmt.Println("\n--- Récupération d'un livre par ID (ID 1) ---")
	retrievedBook, err := GetBookByIDGORM(db, book1.ID)
	if err != nil {
		log.Printf("Erreur lors de la récupération du livre par ID : %v", err)
	} else if retrievedBook != nil {
		fmt.Printf("Livre récupéré : ID: %d, Titre: %s\n", retrievedBook.ID, retrievedBook.Title)
	} else {
		fmt.Println("Livre non trouvé.")
	}

	// --- Mise à jour d'un livre ---
	fmt.Println("\n--- Mise à jour d'un livre (ID 1) ---")
	if retrievedBook != nil {
		retrievedBook.PublishedYear = 1970 // Nouvelle année de publication
		err = UpdateBookGORM(db, retrievedBook)
		if err != nil {
			log.Printf("Erreur lors de la mise à jour du livre : %v", err)
		} else {
			fmt.Printf("Livre ID %d mis à jour.\n", retrievedBook.ID)
		}
	}

	// --- Affichage de tous les livres après mise à jour ---
	fmt.Println("\n--- Tous les livres GORM après mise à jour ---")
	books, err = GetAllBooksGORM(db)
	if err != nil {
		log.Fatalf("Erreur lors de la récupération de tous les livres GORM : %v", err)
	}
	for _, b := range books {
		fmt.Printf("ID: %d, Titre: %s, Auteur: %s, Année: %d, ISBN: %s\n", b.ID, b.Title, b.Author, b.PublishedYear, b.ISBN)
	}

	// --- Suppression d'un livre ---
	fmt.Println("\n--- Suppression d'un livre (ID 2) ---")
	err = DeleteBookGORM(db, book2.ID)
	if err != nil {
		log.Printf("Erreur lors de la suppression du livre : %v", err)
	} else {
		fmt.Printf("Livre ID %d supprimé.\n", book2.ID)
	}

	// --- Affichage de tous les livres après suppression ---
	fmt.Println("\n--- Tous les livres GORM après suppression ---")
	books, err = GetAllBooksGORM(db)
	if err != nil {
		log.Fatalf("Erreur lors de la récupération de tous les livres GORM : %v", err)
	}
	if len(books) == 0 {
		fmt.Println("Aucun livre restant.")
	}
	for _, b := range books {
		fmt.Printf("ID: %d, Titre: %s, Auteur: %s, Année: %d, ISBN: %s\n", b.ID, b.Title, b.Author, b.PublishedYear, b.ISBN)
	}
}
```


**Explications clés pour GORM :**

*   **`gorm.Model` :** Une structure embarquée qui fournit des champs communs comme `ID` (clé primaire auto-incrémentée), `CreatedAt`, `UpdatedAt` et `DeletedAt` (pour la suppression logique). Cela réduit le boilerplate.
*   **Tags GORM (`gorm:"unique"`) :** Permettent de définir des contraintes ou des options spécifiques à la base de données directement dans la structure Go. Ici, `ISBN` est marqué comme unique.
*   **`gorm.Open` :** Initialise la connexion à la base de données. Le driver SQLite est passé via `sqlite.Open()`.
*   **`db.AutoMigrate(&Book{})` :** C'est une fonctionnalité puissante de GORM. Elle compare la structure Go `Book` avec la table existante dans la base de données et effectue les modifications nécessaires (création de table, ajout de colonnes) pour les synchroniser.
*   **Opérations CRUD simplifiées :**
    *   `db.Create(&book)` : Insère un nouvel enregistrement. L'ID généré est automatiquement mis à jour dans l'objet `book`.
    *   `db.First(&book, id)` : Récupère le premier enregistrement correspondant à l'ID. `gorm.ErrRecordNotFound` est l'erreur spécifique si aucun enregistrement n'est trouvé.
    *   `db.Find(&books)` : Récupère tous les enregistrements (ou ceux correspondant à des conditions si ajoutées).
    *   `db.Save(&book)` : Met à jour un enregistrement existant (basé sur l'ID de `book`). Si l'ID est nul, il insère.
    *   `db.Delete(&Book{}, id)` : Supprime un enregistrement par son ID. Si `gorm.Model` est utilisé, GORM effectue une suppression logique (met à jour `DeletedAt`) par défaut, à moins que `Unscoped()` ne soit utilisé.
*   **Gestion des erreurs :** Les méthodes GORM retournent un `*gorm.DB` qui contient un champ `Error`. Il est essentiel de vérifier `result.Error` après chaque opération. `result.RowsAffected` peut être utilisé pour vérifier si l'opération a eu un impact sur des lignes.

Ces solutions vous donnent une base solide pour comprendre et appliquer les deux approches. N'hésitez pas à les exécuter, les modifier et les expérimenter pour approfondir votre apprentissage.