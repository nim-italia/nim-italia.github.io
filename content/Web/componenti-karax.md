---
title: "Nim per il frontend web: componenti Karax"
draft: false
---
***Scritto da [Andrea Manzini](https://ilmanzo.github.io/)***

Abbiamo visto quanto sia semplice con [Karax](https://github.com/karaxnim/karax) creare delle piccole applicazioni interattive, senza scrivere una riga di Javascript. Nella realtà, la programmazione web è molto più complessa ma possiamo semplificare il processo suddividendo le pagine in componenti indipendenti, che poi verranno assemblati ed "usati" nella pagina principale.

In questa guida vedremo dunque un semplice componente che modella un "orologio" autonomo e configurabile dall'esterno, insieme ad una semplice applicazione che sfrutta il componente.
L'idea di fondo è che ogni componente è un oggetto Nim, derivato da una specifica classe *VComponent* (fornita da karax). 

[sul repository](https://github.com/ilmanzo/karax_clock) potete trovare il sorgente completo dell'applicazione e una [live demo](https://ilmanzo.github.io/karax_clock/) funzionante, ma evidenziamo anche qui le parti salienti. Partiamo dal componente:

{{< highlight nim "lineNos=table" >}}
type
  KClock* = ref object of VComponent
    currentTime: DateTime
    offset: TimeInterval
    timer: TimeOut
    prefix: string

# return a VNode with the html rendered for the component
proc render(c: VComponent): VNode =
  let self = KClock(c)
  buildHtml(tdiv):
    let value = format(self.currentTime+self.offset, "HH:mm:ss")
    p:
      text "Local Time " & self.prefix & " => " & value

# update the clock value and re-triggers a timer
proc update(self: KClock) =
  self.currentTime = now()
  self.timer = setTimeout( () => self.update, 100)
  markDirty(self) # need to be re-rendered
  redraw()

# create, initialize and return a new Clock object
proc new*(T: type KClock, tzoffset = 0): KClock =
  let self = newComponent(KClock, render)
  self.currentTime = now()
  self.offset = initTimeInterval(hours = tzoffset)
  self.timer = setTimeout(() => self.update, 100)
  self.prefix = if tzoffset >= 0: "+" else: ""
  self.prefix.add $tzoffset
  return self
{{< / highlight >}}

Questo oggetto avrà almeno:

- alcuni campi per mantenere il suo stato interno 
- un metodo *render* che ritorna la sua rappresentazione HTML 
- un costruttore, per inizializzare gli attributi e creare l'istanza dell'oggetto

in più, nel nostro caso, avremo un metodo *update* (righe 19-23) che aggiorna periodicamente lo stato dell'oggetto e si preoccupa di informare **Karax** che questo nodo del *Virtual Dom* va ridisegnato. La funzione ```setTimeout``` non fa parte di Karax ma è inclusa nella libreria standard, package ```std/dom``` .

Una volta salvato il componente in un file appropriato, non ci resta che "usarlo" in una applicazione web. Per andare un po' oltre i semplici esempi dell'introduzione, includiamo la possibilità di creare e distruggere dinamicamente gli "orologi" tramite pulsanti e di configurare il fuso orario con una casella di input numerico. Nonostante queste funzionalità, l'applicazione rimane leggibile perchè la logica della gestione componenti è incapsulata nella classe separata.

{{< highlight nim "lineNos=table" >}}
var
  clocks: seq[KClock] # keep a list of clocks
  offset: kstring     # value entered in the input box

proc render(): VNode =
  buildHtml(tdiv):
    h2:
      label(`for` = "offset"):
        text "Please enter Timezone offset (-12 .. +12)"
      input(type = "number", id = "offset"):
        proc oninput(ev: Event; n: VNode) =
          offset = n.value
    button:
      text "Add a new Clock"
      proc onclick(ev: Event; n: VNode) =
        let tzofs = parseInt(offset)
        if tzofs >= -12 and tzofs <= 12:
          clocks.add(KClock.new(tzofs))
    button:
      text "Remove last Clock"
      proc onclick(ev: Event; n: VNode) =
        discard clocks.pop()
    for clock in clocks:
      h1:
        clock

setRenderer render
{{< / highlight >}}
