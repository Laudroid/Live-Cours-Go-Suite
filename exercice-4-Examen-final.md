#Langage Go - Examen final

Votre nom :

**1. Go a été créé principalement par des ingénieurs de :**

A. Facebook

B. Microsoft

C. Google

D. IBM

Réponse :

**2. Le fichier source Go doit obligatoirement se terminer par :**

A. .go

B. .golang

C. .java

D. .prog

Réponse :

**3. Quel mot-clé permet de déclarer une variable ?**

A. var

B. let

C. define

D. make

Réponse :

**4. Quel type représente un booléen ?**

A. boolean

B. bl

C. bit

D. bool

Réponse :

**5. Quelle structure de contrôle n’existe PAS en Go ?**

A. if

B. for

C. switch

D. while

Réponse :

**6. Quel package est utilisé pour afficher du texte ?**

A. fmt

B. io

C. string

D. http

Réponse :

**7. Quelle est la fonction d’entrée principale d’un programme Go ?**

A. execute()

B. main()

C. start()

D. run()

Réponse :

**8. Une constante se déclare avec :**

A. static

B. const

C. define

D. immutable

Réponse :

**9. Quelle commande permet d’exécuter un programme Go ?**

A. go exec

B. go run

C. go start

D. go launch

Réponse :

**10. Quelle structure représente une liste dynamique ?**

A. array

B. struct

C. map

D. slice

Réponse :

**11.Expliquez, en 4 ou 5 phrases, la différence principale entre un tableau (array) et un slice (slice) en Go.**

Réponse :

**12. Quelle est la sortie de ce programme ?**

```go
fmt.Println(len([]int{1,2,3,4}))
```

A. 3

B. 4

C. 5

D. Erreur

Réponse :

**13. Quelle structure Go stocke les données sous la forme clé→valeur ?**

A. slice

B. map

C. struct

D. table

Réponse :

**14. Comment obtenir la longueur d’une string ?**

A. size()

B. count()

C. length()

D. len()

Réponse :

**15. Que retourne une fonction Go si rien n'est spécifié ?**

A. 0

B. nil

C. rien

D. undefined

Réponse :

**16. Une fonction peut-elle retourner plusieurs valeurs ?**

A. Oui

B. Non

Réponse :

**17. Quel opérateur obtient l’adresse d’une variable ?**

A. $

B. &

C. @

D. %

Réponse :

**18. Quel mot-clé crée une nouvelle instance d’un type ?**

A. init 

B. make

C. alloc

D. new

Réponse :

**19. Que se passe-t-il dans ce programme ?**

```go
var x *int
fmt.Println(*x)
```

A. Affiche 0

B. Affiche nil

C. Panique (panic)

D. Rien

Réponse :

**20.** Quelle est la valeur par défaut d’un booléen non initialisé ?

A. true

B. false

C. nil

D. 0

Réponse :


**21. Décrivez, en 4 ou 5 phrases, à quoi sert le mot-clé defer et donnez un cas d’usage concret.**

Réponse :


**22. Quelle syntaxe est correcte pour créer un package ?**

A. package main

B. pack main

C. module main

D. namespace main

Réponse :

**23. Quel package standard permet les requêtes HTTP ?**

A. net/http

B. io/http

C. web

D. net/json

Réponse :

**24. Quelle méthode HTTP est utilisée pour créer une ressource ?**

A. GET

B. PUT

C. POST

D. DELETE

Réponse :

**25. Quelle boucle permet de parcourir un slice en Go ?**

A. foreach

B. for index, value := range slice

C. forEach(slice)

D. loop(slice)

Réponse :

**26. Quelle est la sortie ?**

```go
s := make([]int, 0)
s = append(s, 10)
fmt.Println(len(s))
```

A. 0

B. 1

C. 10

D. Erreur

Réponse :

**27. Quel type représente une chaîne de caractères en Go ?**

A. String

B. string

C. char[]

D. text

Réponse :

**28. Quel framework léger est souvent utilisé pour des API REST ?**

A. GoWeb

B. Gin

C. Nectar

D. Spring

Réponse :

**29. Quelle est la sortie ?**

```go
m := map[string]int{"a":1}
fmt.Println(m["b"])
```

A. 1

B. nil

C. 0

D. Erreur

Réponse :

**30. Quel est le résultat ?**

```go
var u struct {
    name string
}
fmt.Println(u.name)
```

A. Panique

B. Erreur compilation

C. Affiche string vide

D. nil

Réponse :

**31. Que fait `defer` ?**
A. Exécute une fonction immédiatement

B. Exécute après une sortie de fonction

C. Annule l’exécution

D. Exécute en parallèle

Réponse :


**32. En 4 ou 5 phrases, expliquez pourquoi les pointeurs sont utiles en Go et dans quelles situations on les privilégie**

Réponse :


**33. Quel est le résultat ?**

```go
func f() (int, error) {
    return 0, nil
}
```

A. Crée une erreur

B. Retourne une erreur vide

C. Retourne (0, nil)

D. Panique

Réponse :


**34. Quelle syntaxe est correcte pour un bloc `if` en Go ?**

A. if (x > 0) { ... }

B. if x > 0 { ... }

C. if x > 0 then { ... }

D. if x > 0: { ... }

Réponse :


**35. Quelle est la fonction d’affichage standard en Go ?**

A. echo()

B. fmt.print()

C. fmt.Println()

D. console.log()

Réponse :


**36. Quelle fonction lit une entrée utilisateur dans la console ?**

A. fmt.Scan()

B. read()

C. input()

D. console.Read()

Réponse :


**37. Quelle est la syntaxe correcte pour accéder à un élément d’une map ?**

A. map.get(key)

B. map[key]

C. map.value(key)

D. map.key

Réponse :

**38. Comment déclarer une fonction qui retourne deux valeurs ?**

A. func f() int, string

B. func f(int string)

C. func f(int, string)

D. func f() (int, string)

Réponse :

**39.** Quelle est la sortie ?

```go
func double(x int) int {
    x = x * 2
    return x
}
a := 5
double(a)
fmt.Println(a)
```

A. 10

B. 5

C. 0

D. Erreur de compilation

Réponse :

**40. Quelle est la particularité des closures en Go ?**

A. Elles s’exécutent une seule fois.

B. Elles peuvent accéder à des variables extérieures à leur scope.

C. Elles ne retournent pas de valeur.

D. Elles doivent être globales.

Réponse :

**41. Quelle est la bonne façon de gérer une erreur en Go ?**

A. try/catch

B. if err != nil { ... }

C. on error do ...

D. catch(err)

Réponse :

**42. En 4 ou 5 phrases, décrivez le concept de goroutine et expliquez en quoi il diffère d’un thread classique.**

Réponse :


**43. Que renvoie `len(slice)` si le slice est vide ?**

A. -1

B. false

C. nil

D. 0

Réponse :

**44. Quelle est la sortie ?**

```go
numbers := []int{1, 2, 3}
numbers = append(numbers, 4)
fmt.Println(numbers)
```

A. `[1 2 3]`

B. `[1 2 3 4]`

C. `[4 1 2 3]`

D. Erreur

Réponse :

**45. Quelle est la sortie ?**

```go
type Person struct {
    Name string
}
p := Person{"Alice"}
fmt.Println(p.Name)
```

A. Alice

B. {Alice}

C. Name: Alice

D. Erreur

Réponse :

**46. Que fait le symbole `&` devant une variable ?**

A. Multiplie la valeur.

B. Renvoie l’adresse mémoire.

C. Crée une copie.

D. Supprime la variable.

Réponse :

**47. Quelle est la sortie ?**

```go
x := 10
p := &x
*p = 20
fmt.Println(x)
```

A. 10

B. 20

C. &20

D. Panic (Erreur)

Réponse :


**48. Quelle est la bonne façon de créer un package en Go ?**

A. Créer un dossier et ajouter `package main` dans le fichier.

B. Utiliser `import newpackage`.

C. Nommer le fichier `package.go`.

D. Créer un dossier et ajouter `package monpackage` dans le fichier.

Réponse :


**49. Quelle est la fonction d’entrée principale d’un programme Go ?**

A. func init()

B. func start()

C. func main()

D. func run()

Réponse :


**50. Quelle instruction importe un package externe ?**

A. use "fmt"

B. require fmt

C. import "fmt"

D. include fmt

Réponse :


**51. Quelle méthode HTTP est utilisée pour récupérer des données ?**

A. POST

B. GET

C. PUT

D. DELETE

Réponse :


**52. Quel package est utilisé pour les requêtes HTTP ?**

A. httpclient

B. net/http

C. io/http

D. web/http

Réponse :


**53. En 4 ou 5 phrases, expliquez le rôle d’un handler dans une API REST.**

Réponse :



**54. Que fait ce code ?**

```go
resp, err := http.Get("https://api.example.com")
if err != nil {
    fmt.Println("Erreur")
}
defer resp.Body.Close()
```

A. Ferme la connexion immédiatement.

B. Ferme la réponse à la fin de la fonction.

C. Ne ferme jamais la connexion.

D. Erreur de compilation.

Réponse :


**55. Quel framework léger est souvent utilisé pour créer des API REST avec Go ?**

A. Django

B. Flask

C. Gin

D. EchoJS

Réponse :


**56. Quelle est la bonne syntaxe pour une boucle infinie en Go ?**

A. while true { ... }

B. do { ... } 

C. loop { ... }

D. for { ... }

Réponse :


**57. Quelle déclaration est incorrecte en Go ?**

A. var a = 10

B. b := 20

C. const c int = 30

D. let d = 40

Réponse :


**58. Quelle est la signature correcte d’une fonction qui retourne deux valeurs (int et error) ?**

A. func calc() (int, error)

B. func calc() int, error

C. func calc(int, error)

D. func calc() error, int

Réponse :


**59. Que renvoie `os.Open("fichier.txt")` si le fichier n’existe pas ?**

A. nil, nil

B. panic

C. fichier vide, nil

D. nil, erreur

Réponse :


**60. Quelle est la sortie de ce code ?**

```go
package main

import (
    "fmt"
    "time"
)

func hello() {
    fmt.Println("Hello")
}

func main() {
    go hello()
    time.Sleep(50 * time.Millisecond)
    fmt.Println("End")
}
```

A. `Hello` suivi de `End`

B. `End` suivi de `Hello`

C. `Hello` uniquement

D. Erreur de compilation

Réponse :

---

**61. Quelle est la sortie de ce code ?**

```go
package main

import (
    "fmt"
)

func printNum() {
    fmt.Println(1)
}

func main() {
    go printNum()
    fmt.Println(2)
}
```

A. Toujours `1` puis `2`

B. Toujours `2` puis `1`

C. `2` seulement

D. Sortie non déterministe

Réponse :


**62. Quelle est la bonne façon d’ignorer une valeur de retour ?**

A. skip, err := div(4, 2)

B. * := div(4, 2)

C. void := div(4, 2)

D. _, err := div(4, 2)

Réponse :


**63. Quelle est la sortie de ce code ?**

```go
package main

import "fmt"

func main() {
    ch := make(chan int)

    go func() {
        ch <- 5
    }()

    v := <-ch
    fmt.Println(v)
}
```

A. 5

B. Rien ne s'affiche (blocage)

C. 0

D. Erreur de compilation

Réponse :

**64.Expliquez, en 4 ou 5 phrases, l'ORM Gorm.**

Réponse :



**65.** Quelle est la sortie de ce code ?

```go
package main

import "fmt"

func main() {
    ch := make(chan string, 1)

    ch <- "Go"
    ch <- "Lang"

    fmt.Println(<-ch)
}
```

A. `Go Lang`

B. `Lang`

C. `Go`

D. Blocage à l’exécution

Réponse :


**66. Quelle est la sortie ?**

```go
type Point struct {
    X, Y int
}
p := Point{X: 3, Y: 4}
fmt.Println(p.X + p.Y)
```

A. 7

B. 34

C. X:3 Y:4

D. Erreur

Réponse :


**67. Que fait ce code ?**

```go
x := 5
p := &x
fmt.Println(*p)
```

A. Affiche 5

B. Affiche l’adresse mémoire

C. Affiche &x

D. Erreur

Réponse :


**68. Que fait ce code ?**

```go
type Counter struct {
    value int
}
func (c *Counter) Increment() {
    c.value++
}
```

A. Définit une méthode qui modifie la copie du compteur.

B. Définit une méthode qui modifie le pointeur du compteur.

C. Définit une fonction anonyme.

D. Provoque une erreur de compilation.

Réponse :

**69.** Quelle est la sortie ?

```go
type Item struct { Name string }
func (i Item) Show() { fmt.Println(i.Name) }

obj := Item{"Book"}
obj.Show()
```

A. Book

B. i.Name

C. {Book}

D. Erreur

Réponse :


**70.** Quelle est la différence entre une méthode avec récepteur valeur et pointeur ?

A. Le pointeur ne peut pas appeler la méthode valeur.

B. La méthode pointeur peut modifier la structure originale.

C. Il n’y a pas de différence.

D. La méthode valeur est plus lente.

Réponse :


**71.** Où doit se trouver la fonction `main()` ?

A. Dans le package `main`

B. Dans le package `core`

C. Dans n’importe quel package

D. Dans un fichier `run.go`

Réponse :


**72.** Quelle commande initialise un module Go ?

A. go init

B. go module create

C. go start

D. go mod init

Réponse :


**73.** Quelle est la sortie ?

```go
func main() {
    defer fmt.Println("defer")
    fmt.Println("main")
}
```

A. `defer` puis `main`

B. `main` puis `defer`

C. `main defer` sur une ligne

D. Erreur

Réponse :


**74.** Quelle est la sortie ?

```go
r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{"message": "pong"})
})
```

A. Retourne `"pong"` au format JSON.

B. Retourne `"pong"` en texte brut.

C. Retourne `"ping"` en JSON.

D. Erreur de compilation.

Réponse :


**75.** Que fait `defer resp.Body.Close()` après un `http.Get()` ?

A. Ferme la réponse immédiatement.

B. Efface la mémoire allouée.

C. Ignore le corps de la réponse.

D. Ferme le corps de la réponse à la fin de la fonction.

Réponse :



