# 2-Concurrence et Web temps réel

## 2-Gestion des opérations simultanées

### 1-Utiliser les goroutines pour lancer des tâches

---

Go intègre la notion de **goroutine**, qui permet de lancer très facilement des tâches en parallèle. Les goroutines sont des unités légères d’exécution concurrente, gérées par l’ordonnanceur Go et non par le système d’exploitation. Elles offrent un moyen simple et efficace pour gérer des opérations simultanées.

---

## Qu’est-ce qu’une goroutine ?

Une goroutine est une fonction ou méthode qui s’exécute indépendamment de la goroutine appelante. La création se fait en préfixant un appel de fonction par le mot-clé `go` :

```go
go fonction()
```

Le programme ne bloque pas et la goroutine s’exécute en arrière-plan.

---

## Exemple simple de goroutine

```go
package main

import (
    "fmt"
    "time"
)

func afficheMessage(msg string) {
    for i := 0; i < 3; i++ {
        fmt.Println(msg)
        time.Sleep(200 * time.Millisecond)
    }
}

func main() {
    go afficheMessage("Goroutine 1")
    go afficheMessage("Goroutine 2")

    // Attendre un peu pour que les goroutines s’exécutent
    time.Sleep(1 * time.Second)
    fmt.Println("Programme principal terminé")
}
```

Dans cet exemple, deux goroutines s’exécutent simultanément, affichant des messages en alternance avec la goroutine principale.

---

## Attention à la terminaison du programme

Les goroutines sont lancées de manière non bloquante. Si la fonction `main` se termine avant elles, le programme quitte immédiatement, interrompant toutes les goroutines en cours.

Pour éviter cela, on utilise généralement :

- **`time.Sleep`**, peu recommandé car imprécis.
- **Channel**, pour synchroniser explicitement.
- **`sync.WaitGroup`**, pour attendre la fin de plusieurs goroutines.

---

## Exemple d’utilisation de `sync.WaitGroup`

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

func travaille(id int, wg *sync.WaitGroup) {
    defer wg.Done()
    fmt.Printf("Goroutine %d démarre\n", id)
    time.Sleep(time.Second)
    fmt.Printf("Goroutine %d termine\n", id)
}

func main() {
    var wg sync.WaitGroup

    for i := 1; i <= 3; i++ {
        wg.Add(1)
        go travaille(i, &wg)
    }

    wg.Wait() // Attend que toutes les goroutines aient terminé
    fmt.Println("Tous les travaux sont terminés")
}
```

Le `WaitGroup` améliore le contrôle pour ne passer à la suite qu’après la fin effective des tâches lancées.

---

## Goroutine vs Thread

| Aspect                    | Goroutine                          | Thread OS                     |
|---------------------------|----------------------------------|-------------------------------|
| Création                  | Quelques microsecondes            | Plusieurs millisecondes       |
| Empilement mémoire        | Quelques Ko                      | Plusieurs Mo                  |
| Ordonnancement            | Ordonnanceur Go                  | Ordonnanceur OS               |
| Communication            | Channels intégrés et synchronisation légère | IPC ou mutex, plus lourds     |
| Scalabilité               | Très haute (des milliers/millions possibles) | Moins élevé                   |

---

## Diagramme Mermaid : Cycle de vie simple d’une goroutine

```mermaid
flowchart TD
    A[main start] --> B[go fonction()]
    B --> C{Goroutine active ?}
    C -- Oui --> D[Exécution de la fonction]
    D --> E[Fin de la goroutine]
    E --> F[main attend ou continue son exécution]
    C -- Non --> F
```

---

## Bonnes pratiques

- Ne pas lancer de goroutines sans mécanisme de synchronisation pour éviter la terminaison prématurée.
- Utiliser `WaitGroup` pour gérer facilement plusieurs goroutines.
- Éviter les goroutines non contrôlées qui peuvent causer des fuites mémoire.
- Lorsque la communication entre goroutines est nécessaire, utiliser les channels.
- Limiter le nombre de goroutines à ce que la machine peut supporter ou utiliser un pool de goroutines.

---

## Sources et références

- [Effective Go – Concurrency](https://go.dev/doc/effective_go#goroutines)
- [Go by Example – Goroutines](https://gobyexample.com/goroutines)
- [Go by Example – WaitGroups](https://gobyexample.com/waitgroups)
- [Go Blog – Goroutines](https://blog.golang.org/goroutines)
- [Official Go Blog – Concurrency is not Parallelism](https://blog.golang.org/concurrency-is-not-parallelism)

---

Lancer des tâches simultanément est simple avec les goroutines. Leur faible coût et leur gestion intégrée en font un outil puissant pour la programmation concurrente en Go. Associer ces goroutines avec des mécanismes de synchronisation évite les pièges liés à l’exécution concurrente.