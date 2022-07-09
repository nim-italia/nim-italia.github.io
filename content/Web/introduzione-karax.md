---
title: "Nim per il frontend web: introduzione a Karax"
draft: false
---
***Scritto da [Andrea Manzini](https://ilmanzo.github.io/)***

### introduzione

[Karax](https://github.com/karaxnim/karax) è un web framework che sfrutta il target *javascript* di Nim e un po' come i software più famosi come React, Vue o Angular, ci permette di creare SPA (Single Page Applications) con il nostro linguaggio preferito, programmando quindi applicazioni web direttamente in Nim. La peculiarità di queste applicazioni è che vengono eseguite totalmente lato browser, per cui possono essere servite da un semplice file HTML statico.
Se invece utilizziamo Nim lato server, ad esempio in accoppiata con un framework backend, ad esempio [Jester](https://github.com/dom96/jester) o [Prologue](https://github.com/planety/prologue) possiamo gestire un completo full-stack per lo sviluppo web sempre usando lo stesso linguaggio.

Il [Nim Playground](https://play.nim-lang.org/) e il [Nim Forum](https://forum.nim-lang.org/) ad esempio sono scritti completamente in Nim.

L'installazione di Karax è molto semplice, basta un 

    nimble install karax

che, oltre alla libreria, ci mette anche a disposizione un comodo eseguibile per provare i nostri progetti, chiamato 'karun'.

Questa semplice pagina non vuole essere una documentazione esaustiva ma un invito a provare questa tecnologia e contribuire con idee e lavori.

### primi passi


{{< highlight nim >}}
include karax/prelude        # scorciatoia per importare alcuni moduli karax tutti insieme

proc createDom(): VNode =    # definisce una funzione che ritorna un nodo HTML
  buildHtml(p):              # crea un elemento di tipo "paragrafo"
    text "Ciao da Karax!" # setta il testo nell'elemento paragrafo

setRenderer createDom        # indica a Karax quale funzione usare per renderizzare la pagina
{{< / highlight >}}

salviamo il file come ```index.nim``` e lanciamo il comando

    karun -r index.nim

il sorgente sarà compilato in javascript, inserito automaticamente in una pagina HTML che verrà servita da un piccolo webserver provvisorio. Contestualmente si aprirà la pagina web sul browser predefinito.

### loop e condizioni

la funzione che viene invocata è codice Nim a tutti gli effetti, e quindi abbiamo a disposizione tutti i costrutti del linguaggio. Il DSL di Karax rende molto semplice scrivere cicli su collezioni e sequenze

{{< highlight nim >}}
include karax/prelude
var list = @[kstring"Mela", "Arancia", "Banana"]
proc createDom(): VNode =
  buildHtml(tdiv):
    for fruit in list:
      p:
        text fruit
        text " è un frutto"

setRenderer createDom
{{< / highlight >}}

### eventi ed interazioni

Una SPA necessita di essere interattiva, reagendo agli input dell'utente senza dover contattare il server e ricaricare tutto quanto ma solamente gli elementi che vengono modificati; Karax implementa un Virtual Dom per tenere traccia di cosa va ridisegnato. Come in javascript, possiamo associare codice da eseguire al verificarsi di un evento.

{{< highlight nim >}}
include karax/prelude
import algorithm
var message = "Messaggio di prova"
proc createDom(): VNode =
  buildHtml(tdiv):
    p:
      text message
    button:
      text "Clicca per rovesciare!"
      proc onclick =
        message.reverse()

setRenderer createDom
{{< / highlight >}}


### input utente

In questo esempio vediamo l'utilizzo del tipo *kstring* ; quando si compila con target js, è sinonimo di *cstring*, mentre con target nativo diventa *string*. Questo accorgimento è necessario perchè nei due linguaggi [i tipi stringa sono diversi](https://ssalewski.de/nimprogramming.html#_strings)

{{< highlight nim >}}
include karax/prelude
var message = kstring"Karax funziona"
proc createDom(): VNode =
  buildHtml(tdiv):
    tdiv:
      p:
        text message
    input(value = message):
      proc oninput(e: Event, n: VNode) =
        message = n.value

setRenderer createDom
{{< / highlight >}}

osserviamo anche che per creare un elemento ```<DIV>``` dobbiamo usare ```tdiv``` perchè in nim la parola ```div``` è riservata all'operatore di divisione.

Nella prossima parte vedremo come rendere modulare lo sviluppo sfruttando la possibilità di creare dei **componenti** autonomi e riusabili.


### link per approfondimenti

- [Blog post originale](https://nim-lang.org/araq/karax.html)
- [Karax Intro](https://github.com/karaxnim/karax/blob/master/guide/Introduction.md)
- [Exploring Karax](https://moigagoo.svbtle.com/exploring-karax)
- [Using Nim for Web Development in 2021](https://arhamjain.com/2021/11/22/nim-webdev.html)
