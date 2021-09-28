---
title: "Raspberry Pico RP2040"
date: 2021-09-21T19:19:32+02:00
draft: false
---
***Scritto da [Andrea Martin](https://sites.google.com/view/martinix/home)***

il Raspberry Pico è una minucscola scheda che monda un piccolo, ma potente microprocessore dalle seguenti caratteristiche:
- Processore Dual-Core Arm Cortex M0+.
- Clock da 133Mhz.
- 264Kb Sram e 2MB di falsch Memory on board.
- Usb 1.1
- 26 pin I/O (multifunzione).
- Canali PWM, IC2, Dac-12bit.
- e molto altro...

La programmazione "naturale" di questo dispositivo è ovviamente in C, ma ne parliamo qui perchè si stà sviluppando un wrapper per poterlo utilizzare anche con Nim. Ovviamente ce ancora molto lavoro da fare, ma con l'ultima versione della libreria "picostdlib" si son fatti molti passi avanti, sopratutto sulla semplicità di avviare, compilare il nostro progetto, e non solo, abbiamo aggiunto funzionalità che potrebbero essere interessanti, qui puoi trovare la libreria:

picostdlib link: <https://github.com/beef331/picostdlib>

Oltre al controllo dei pin (input, outup, pullup, pulldown) in modalità classica,ultimamnete si son aggiunti due importanti funzioni per un microntrollore:
- La comunicazione in ingresso tramite usb, per mandare comandi al microcontrollore.
- l'attivazione del bas i2c importante per dialogare con un infinità di hardware (bussole, accelerometri display ecc).
- modalità "sugar" per inizializzare i pin I/O del micro.

Al momento ho anche sviluppato una semplice libreria per far leggere al micro, non solo i caratteri singoli inviati via usb, ma di prendere l'intera stringa inviata, cosa molto utile per i protocolli di comunicazione verso lo stesso micro, in più sto scrivendo la libreria per un PCF8574 ovvero un I/O expander, ma sono ancora in sviluppo. Diseguito un breve esempio:

{{< highlight nim >}}

import picostdlib/[gpio, i2c]
import picostdlib

const 
  on = true
  off = false
  p0: uint8 = 0b00000001
  p1: uint8 = 0b00000010
  p2: uint8 = 0b00000100
  p3: uint8 = 0b00001000
  p4: uint8 = 0b00010000
  p5: uint8 = 0b00100000
  p6: uint8 = 0b01000000
  p7: uint8 = 0b10000000

type 
  Pcf8574 = ref object 
    addressDevice: uint8
    blockk: I2cInst
    buffer: uint8

proc writeBytex(self: Pcf8574, dato:uint8 ) =
  let dato = dato.unsafeAddr
  writeBlocking(self.blockk, self.addressDevice, dato,1, true)

proc digitaWrite(self:Pcf8574,pin:uint8, value:bool) =
  if value == on:
    self.buffer = (self.buffer or pin)
    writeBytex(self,self.buffer)
  elif value == off:
    self.buffer = (self.buffer and pin)
    writeBytex(self,self.buffer)


when isMainModule:
  stdioInitAll()
  let expander = Pcf8574(addressDevice: 0x20, blockk: i2c0, buffer: 0b00000000)

  const sda = 0.Gpio 
  const scl = 1.Gpio 
  const address = 0x20
  var buffer:uint8 = 0b00000000

  init(i2c0,10000)
  sda.setFunction(I2C); sda.pullUp()
  scl.setFunction(I2C); scl.pullUp()


  let dato = buffer.unsafeAddr
  i2c0.writeBlocking(address, dato,  1, true)
  sleep(1000)

  while true:
    digitaWrite(expander,p1,on)
    sleep(1500)
    digitaWrite(expander,p4,on)
    sleep(1500)
    digitaWrite(expander,p1,off)
    sleep(1500)
    digitaWrite(expander,p4,off)
    sleep(1500)

{{< / highlight >}}

Ho anche fatto un piccolo scanner I2C per trovare gli indirizzi dei vari dispositivi che son sempre un incubo. qui puoi trovare le librerie/utility:

<https://github.com/Martinix75/Raspberry_Pico.git>

ma sono ancora allo stato iniziale.