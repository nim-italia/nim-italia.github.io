---
title: "Array e Sequenze"

draft: false
---
***Scritto da [Daniele (dadadani)](https://github.com/dadadani)***

Su Nim esistono due principali tipi per gestire una lista di elementi: gli **array** e le **sequenze**. Anche se il loro obbiettivo è immagazzinare più elementi, i due oggetti hanno alcune differenze che li rendono utilizzabili in ambiti specifici.

### Array

Gli array su Nim sono simili a quelli del linguaggio C, sono quindi allocati nella memoria stack e hanno sempre una dimensione specifica e non modificabile.

{{< highlight nim >}}
var primoArray = ["ciao"] # Definizione implicita, possiamo definire fin da subito degli elementi

var secondoArray: array[3, string] # Definizione esplicita
secondoArray[0] = "a"
{{< / highlight >}}

La lunghezza di un array **deve essere conosciuta prima o durante il processo di compilazione** (ad esempio con l'utilizzo di macro), quindi non è possibile creare array con una dimensione che verrà conosciuta durante l'esecuzione del programma.

{{< highlight nim >}}
const LUNGHEZZA = 2*4

var def: array[LUNGHEZZA, string] # Definizione corretta, il calcolo verrà fatto durante la compilazione

var lunghezzaDinamica = 4*4

var sbagliato: array[lunghezzaDinamica, string] # Definizione sbagliata, il calcolo non può essere fatto durante l'esecuzione del programma
{{< / highlight >}}

### Sequenze

Le sequenze si possono considerare degli array "potenziati", in quanto hanno il grande vantaggio di essere dinamici, cioè **non avere una lunghezza specifica**.

{{< highlight nim >}}
var primaSequenza = @["nim", "brutto"] # Definizione implicita
delete(primaSequenza, 1)
add(primaSequenza, @["è", "bello"])

var secondaSequenza = newSeq[int]() # Definizione esplicita
secondaSequenza.add(1)
secondaSequenza[0] = 15
{{< / highlight >}}


Possiamo anche modificare il contenuti di una sequenza che viene passata in una procedura attraverso la parola chiave `var` nei parametri:

{{< highlight nim >}}
proc modificaSequenza(data: var seq[string]) =
    data.add("modificata")

var nuovaSequenza = @["sequenza"]
modificaSequenza(nuovaSequenza) # Diventerà @["sequenza", "modificata"]
{{< / highlight >}}


Se vogliamo rendere immutabile (cioè non modificabile) una sequenza, lo possiamo fare attraverso l'utilizzo di `let`:

{{< highlight nim >}}
let immutabile = @["non puoi toccarmi"]
immutabile.add("e invece si") # Non accettabile
{{< / highlight >}}

`for` permette l'utilizzo di sequenze definite in modo implicito:

{{< highlight nim >}}
for elemento in @[1, 45, 3]:
  echo elemento
{{< / highlight >}}

### Varargs

Concludiamo questa panoramica con un tipo speciale di Nim.
Quando dobbiamo leggere più valori in un solo parametro, possiamo utilizzare `varargs`, ci permette di ottenere più elementi in modi diversi, ad esempio con l'utilizzo di una sequenza o di un array.
{{< highlight nim >}}
proc leggiLista(oggetti: varargs[string]) =
  for oggetto in oggetti:
    echo oggetto

leggiLista "diversi", "modi"
leggiLista(@["stesso", "oggetto"])
leggiLista(["facile", "da", "usare"])
{{< / highlight >}}

