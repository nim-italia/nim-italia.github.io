---
title: "Alias, Strutture Dati, Puntatori e Riferimenti"
draft: false
---
***Scritto da [Raffaele](https://rc-05.github.io/)***


## Alias

Un alias è semplicemente un modo per poter fare riferimento ad un tipo
esistente tramite un altro nome.

Se si utilizza la parola chiave `distinct` si definisce un tipo distinto ma che
possiede lo stesso layout del tipo "sorgente".

{{< highlight nim >}}
type
  Foo = uint
  ## Alias per un numero intero senza segno.

  Bar = distinct uint
  ## Definizione di un tipo distinto da uint ma che ha lo stesso layout
  ## in memoria.
{{< / highlight >}}

## Strutture Dati

Nim permette di creare una struttura dati tramite `object`.

{{< highlight nim >}}
type
  Person = object
    name: string
    age: int

when isMainModule:
  let marco = Person(name = "Marco", age = 20)
{{< / highlight >}}

## Puntatori

Nim possiede due tipi di puntatori, tracciati e non.

I puntatori non tracciati vengono dichiarati con `ptr` e devono
essere gestiti dal programmatore.

{{< highlight nim >}}
type
  Person = object
    name: string
    age: int

  PersonPtr = ptr Person
  ## Crea un puntatore all'oggetto Person.
{{< / highlight >}}

I puntatori hanno un tipo, ma possono essere implicitamente convertiti in
`pointer`: quest'ultimo non dovrebbe essere usato se non per interfacciarsi
con un puntatore proveniente da C, quindi ad esempio in una libreria scritta
in C.

## Riferimenti

Nim mette a disposizione, come già anticipato, anche dei puntatori tracciati
dal runtime, quindi non necessitano di essere gestiti dal programmatore.
Essi vengono dichiarati con `ref`.

{{< highlight nim >}}
type
  Person = object
    name: string
    age: int

  PersonRef = ref object
  ## Crea un riferimento tracciato all'oggetto Person.
{{< / highlight >}}

I puntatori tracciati (o riferimenti) possono essere usati come parametri di
funzioni per permettere di passare direttamente un riferimento ad un oggetto
anziché effettuare una copia di esso ad ogni chiamata: questo risulterà
fondamentale per strutture dati particolarmente grandi.

I riferimenti hanno una relazione uno a molti, nel senso che più riferimenti
possono accedere allo stesso oggetto in memoria sia in lettura che
in scrittura quindi attenzione ad usarli in programmi multi-threaded.