# **TP Go : Concurrence & Communication**

# **Exercice 1 — Lancer des goroutines**

Créez un programme qui lance **3 goroutines** affichant chacune un message toutes les 200ms pendant 1 seconde.

### Consigne

1. Créez une fonction `worker(name string)`

   * Elle affiche `name` et un timestamp toutes les 200ms.
   * Après 5 affichages, elle se termine.

2. Dans le `main`

   * lancez `worker("A")`, `worker("B")`, `worker("C")` en goroutines.

3. Utilisez un **WaitGroup** pour attendre leur fin.

---

# **Exercice 2 — Channels simples **

Modifiez le programme pour **récupérer les résultats** via un channel au lieu d’un `Println`.

### Consigne

1. La fonction `worker` doit envoyer une chaîne de caractère formatée (`fmt.Sprintf`) dans un channel `chan string`.
2. Le `main` doit lire le channel et afficher les messages dans la console.
3. Fermez le channel lorsque tout est terminé.

Astuce : le producteur écrit → consommateur lit.

---

# **Exercice 3 — Multiplexage avec `select`**

Vous allez maintenant créer **2 types de workers** écrivant dans **2 channels différents**, et multiplexer leur lecture.

### Consigne

1. Créez :

   * `workerFast`: écrit toutes les **100ms**
   * `workerSlow`: écrit toutes les **250ms**

2. Chaque worker écrit 10 messages dans son channel.

3. Dans le `main`, utilisez `select` :

```go
select {
case msg := <-fastCh:
    fmt.Println("FAST:", msg)
case msg := <-slowCh:
    fmt.Println("SLOW:", msg)
}
```

4. Arrêtez proprement lorsque les deux workers sont terminés.

📌 Objectif : observer l'interleaving des messages.

---

# **Exercice 4 — Timeout optionnel **

Ajoutez un **timeout** dans le `select` :

```go
case <-time.After(300 * time.Millisecond):
    fmt.Println("⏱️ Timeout: aucun message reçu")
```

---

# **Structure attendue**

```
tp-concurrency-go/
 ├─ main.go
 ├─ worker.go (optionnel)
```

