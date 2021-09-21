# Funzioni

Le funzioni sono il metodo fondamentale per poter dividere un programma in
più "sotto-programmi": per dichiarare una funzione si usa `proc`.

{% highlight nim %}
# Dichiaro una funzione che prende due numeri interi con segno come
# parametri e restituisce un intero.
proc somma(a, b: int): int =
  result = a + b
{% endhighlight %}

È possibile notare una variabile di nome `result` all'interno della funzione:
quella è la variabile implicita usata per restituire il valore alla funzione
chiamante, ma è possibile anche usare `return <valore>` per restituire il
valore.

>Consiglio vivamente di creare molte funzioni dove ciascuna delle quali
>fa esattamente una ed una sola cosa per poi "concatenarle" tra di loro per
>ottenerne una più complessa.

I parametri delle funzioni sono di default immutabili: per renderli modificabili
bisogna usare `var`.

{% highlight nim %}
proc foo(obj: Foobar) =
  obj.value = 50 # Errore, non è possibile modificare un parametro immutabile.

proc bar(obj: var Foobar) =
  obj.value = 10 # Questo lo puoi fare
{% endhighlight %}

Quando viene chiamata una funzione i parametri vengono passati per copia,
questo può impattare pesantemente le prestazioni del programma se si ha a che
fare con strutture dati grandi.

In questo caso possiamo lavorare con i riferimenti.

{% highlight nim %}
proc foo(obj: ref Foobar) =
  obj.value = 50
{% endhighlight %}
