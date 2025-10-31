# 2-Concurrence et Web temps réel

## 1-Introduction à la concurrence en Go

### 2-Synchronisation et communication entre goroutines

---

Gérer plusieurs goroutines s’exécutant simultanément soulève deux défis essentiels : **la synchronisation** et **la communication**. Go propose des outils natifs pour répondre à ces besoins : les **channels** pour échanger des données de manière sécurisée et les primitives du package `sync` pour contrôler l’accès aux ressources partagées.

---

## 1. Synchronisation via les channels

Les channels jouent un double rôle : communication et synchronisation. L’envoi (`ch <- val`) et la réception (`val := <- ch`) sont des opérations bloquantes en l’absence de buffer, assurant ainsi automatiquement la synchronisation.

### Exemple : synchronisation simple via channel unbuffered

```go
package main

import "fmt"

func worker(done chan bool) {
    fmt.Println("Travail en cours...")
    done <- true // signal de fin
}

func main() {
    done := make(chan bool)
    go worker(done)
    <-done // attente du signal
    fmt.Println("Worker terminé")
}
```

Ici la goroutine `worker` envoie sur le channel quand son exécution est terminée. La goroutine principale attend la réception : synchronisation garantie sans mutex.

---

## 2. Utilisation des primitives du package `sync`

Parfois, la synchronisation doit contrôler un état partagé au-delà du simple signal.

### Mutex (`sync.Mutex`)

Permet d’éviter l’accès concurrent à une ressource critique.

```go
import (
    "fmt"
    "sync"
)

var (
    compteur int
    mu       sync.Mutex
)

func increment(wg *sync.WaitGroup) {
    mu.Lock()
    compteur++
    mu.Unlock()
    wg.Done()
}

func main() {
    var wg sync.WaitGroup
    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go increment(&wg)
    }
    wg.Wait()
    fmt.Println("Valeur finale :", compteur)
}
```

Sans mutex, `compteur` pourrait avoir une valeur incorrecte à cause des conditions de course.

### WaitGroup (`sync.WaitGroup`)

Permet d’attendre la fin de plusieurs goroutines.

- `wg.Add(n)`: nombre de goroutines à attendre.
- `wg.Done()`: signale la fin d’une goroutine.
- `wg.Wait()`: bloque jusqu’à ce que toutes les goroutines aient appelé `Done`.

---

## 3. Communication multipoints avec `select`

`select` permet de surveiller plusieurs channels simultanément et d’agir selon celui qui est prêt.

```go
select {
case msg1 := <-chan1:
    fmt.Println("Reçu sur chan1 :", msg1)
case msg2 := <-chan2:
    fmt.Println("Reçu sur chan2 :", msg2)
case <-time.After(2 * time.Second):
    fmt.Println("timeout")
}
```

`select` permet de construire des goroutines réactives sur plusieurs sources.

---

## 4. Exemple combiné : producteur-consommateur sécurisé

```go
package main

import (
    "fmt"
    "sync"
)

func producer(ch chan<- int, wg *sync.WaitGroup) {
    for i := 0; i < 5; i++ {
        ch <- i
    }
    close(ch)
    wg.Done()
}

func consumer(ch <-chan int, wg *sync.WaitGroup) {
    for val := range ch {
        fmt.Println("Consommé :", val)
    }
    wg.Done()
}

func main() {
    ch := make(chan int)
    var wg sync.WaitGroup

    wg.Add(2)
    go producer(ch, &wg)
    go consumer(ch, &wg)

    wg.Wait()
    fmt.Println("Fin du programme")
}
```

---

## Diagramme Mermaid : Synchronisation et communication

```mermaid
flowchart LR

subgraph Goroutine 1 (Producteur)
    A1[Produit données]
    A2[Envoie sur channel]
end

subgraph Channel
    B1[Buffer ou mémoire]
end

subgraph Goroutine 2 (Consommateur)
    C1[Reçoit données du channel]
    C2[Traite données]
end

A1 --> A2 --> B1
B1 --> C1 --> C2
```

---

## Conseils pratiques

- Préférer **channels** pour synchroniser quand il s’agit de transfert de données.
- Utiliser un **mutex** pour protéger une ressource partagée sans échange de données.
- Utiliser `WaitGroup` pour attendre la fin d’exécution de plusieurs goroutines.
- Tester et analyser les conditions de course avec l’outil `go run -race`.
- `select` permet d’écrire des goroutines multiplexées, très utiles en cas d’IO ou timers.

---

## Sources

- [Effective Go – Concurrency](https://go.dev/doc/effective_go#concurrency)
- [Go by Example – Channels](https://gobyexample.com/channels)
- [Go by Example – Mutex](https://gobyexample.com/mutex)
- [Go by Example – WaitGroups](https://gobyexample.com/waitgroups)
- [Official Go blog – Concurrency is not parallelism](https://blog.golang.org/concurrency-is-not-parallelism)
- [Go documentation – sync package](https://pkg.go.dev/sync)

---

La synchronisation et la communication sont les piliers d’une programmation concurrente robuste en Go. Leur maîtrise permet de construire des applications parallèles performantes tout en évitant les pièges classiques des accès concurrents.