---
title: "dividere per zero in Nim"
draft: false
---
***Scritto da [Andrea Manzini](https://ilmanzo.github.io/)***

### "verso l'infinito, e oltre"

Storicamente nei calcolatori la divisione per zero, in quanto operazione impossibile, è sempre stata gestita in modo problematico. I primi calcolatori elettromeccanici si inceppavano ed entravano in un [loop infinito](https://www.youtube.com/watch?v=WLFVZXhn1Qc), dal quale si poteva uscire solo con una [apposita operazione](https://www.youtube.com/watch?v=7Kd3R_RlXgc) o spegnendo e riaccendendo tutta la macchina, che a causa dello sforzo richiesto rischiava anche di deteriorarsi e/o subire danni fisici.

In Nim la situazione non è così drammatica ma può portare ad alcune sorprese. Anzitutto se proviamo con un

{{< highlight nim >}}
let x=1/0
echo "Risultato=", x

Risultato=inf
{{< / highlight >}}

a primo impatto otteniamo un valore *infinito* , che ci da anche problemi nel caso volessimo testare tale eventualità. Non possiamo infatti confrontare nessun numero con "inf" perchè sarebbe un simbolo non contemplato dal linguaggio.

Un modo corretto per identificare il caso, o meglio *classificarlo*, potrebbe essere ad esempio

{{< highlight nim >}}
import math
for n in countup(-5,5):
  let x=100/n  
  if x.classify == fcInf:
    echo "hai diviso per zero"  
  else:    
    echo "Risultato=",x
{{< / highlight >}}

questa apparente *"stranezza"* viene dal fatto che la divisione con l'operatore **/** è sempre una divisione float, e dato che Nim rispetta [lo standard per il calcolo a virgola mobile IEEE 754](https://it.wikipedia.org/wiki/IEEE_754), otteniamo il risultato previsto dallo standard. Per chi volesse approfondire la questione, [lascio un link al documento](https://people.eecs.berkeley.edu/~wkahan/ieee754status/IEEE754.PDF).

Se volessimo un comportamento alternativo, possiamo ad esempio utilizzare l'operazione di divisione intera con l'operatore **div** :

{{< highlight nim >}}
import math
for n in countup(-5,5):
  let x=100 div n  
  echo "Risultato=",x
{{< / highlight >}}

eseguendo questo codice, otteniamo un errore sotto forma di **Exception**, che a questo punto saremo in grado di [gestire](https://nim-lang.org/docs/tut2.html#exceptions) con il costrutto ``try .. except`` del linguaggio.

{{< highlight nim >}}
Error: unhandled exception: division by zero [DivByZeroDefect]
{{< / highlight >}}

come considerazione finale: anche Hitler ha imparato a sue spese che non si può dividere per zero ...

{{< youtube FuTz3NL32AM >}}

