---
title: "Display1602"
draft: false
--- 
***Scritto da [Andrea Martin](https://sites.google.com/view/martinix/home)***

In questo articolo, presento la libreria per gestire i display tipo 1602 pilotati via I2C. Lo ammetto questa libreria non è interamente farina del mio sacco, in quanto son partio dalle equivalenti in microPython per poi scriverle in Nim. Di mio ho aggiunto però delle procedure per gestire le stringhe sul display, ad esempio la centratura e lo scorrimento delle stesse. Se scrivere la libreria è stato comuqnue abbastanza complesso, usarla per visualizzare le nostre cose, spero di averla resa semplice. Vediamo le procedure (pubbliche) che si possono usare:

1. **initDysplay**: inizializza il display fornendo alla libreria il blocco (i2c0 o i2c1) , lindirizzo del dispositivo, il numero di colonne ed il numero di righe disponibili, dati molto importanti per il corretto funzionamento, quindi prestare attenzione.
>*esempio: let lcd = initDisplay(i2c = i2c1, lcdAdd = 0x27, numColum = 16, numLines = 2)*

2. **clear**: ripulisce tutto il display eliminando ogni carattere.
>*esempio: cld.clear()*

3. **clearLine**: A differenza di "clear" che pulisce tutto, questa procedura pulisce solo la line su cui è posizionato il cursore.
>*esempio lcd.clearLine()*

4. **moveTo**: Muove il cursore sia sulle colonne del display sia sulle righe, andando a posizionarlo ad esempio nella corretta posizione dove si desidera stampare un carattere.
>*esempio: lcd.lcd.moveTo(10,1) sposterà il cursore nella 11 colonna della 2 linea si paret a contare da 0)

5. **putString**: Stamperà una stringa sul display se essa è composta dallo stessno numero di caratteri di quante colonne abbia il display in uso posizionandola a sinistra, altrimenti la farà muovere verso sinistra.
>*esempio: lcd.putString("Ciao!")*

6. **putChar**: Stamperà un singolo carattere, questa procedura potrebbe sembrare suprflua, ma come vedremo dopo sarà moltu utile qaundo dovremmo stampare i nostri caratteri personali.
>*esempio: lcd.putChar('@')*

7. **centerString()**: Stamperà la stringa data esattamente al centro del display.
>*esempio: lcd.centerString("Ciao Nim!")

8. **shiftChar()**: Muove attraverso una riga il carattere passato, questa funzione ha due parametri oltre al carattaere che volgiamo visualizzare: *speed (uint16)* che dice a che velocità muovere il carattere e *dir (bool)* che dice se fralo andare da desta verso sinistra o viceversa.
>*esempio: lcd.shiftChar('>', speed = 400, dir = true) i valori qui proposti sono quelli di default.*

9. **shiftString**: é molto simile a shiftChar, solo che tratat direttamente intere stringhe. In questa procedura si puo passere come parametri: *speed (uint16)* che dice a che velocità muovere il carattere e *dir (bool)* che dice se fralo andare da desta verso sinistra o viceversa ed *cross (bool)* indica se la stringa che va a formarsi deve rimanare li dove si forma oppure se essere trascinata dall'altro lato del display  *effect (uint8)* che per ora stabilisce se la stringa deve "uscire" dal display o fermarsi sul bordo.
>*esempio: lcd.shiftString(disp1602Ver, cross = true, effect = 1)*

10. **customChar**: permette di create dei nostri caratteri personali da mandare poi al display ed essere aggiunti a quelli "di serie" (non vengono salvati permanentemente!!). Vedremo che crearli è davvero molto semplice, basta creare un array di 8 elementi con valore compreso tra 0 e 31 (0x00 -> 0x1f; 5 pixel) la libreria è stata ideata per accettare i dati in qualsiasi formato (decimale, esadecimale, binario).Disegnato il nostro carattere  dobbiamo scriverlo nella memoria del display (**IMPORTANTE**:non visualizzarlo ancora, solo scriverlo!!) e gli daremo un numero di riferimento, nel nostro caso "0" ed ovviamnte indichiamo quale carattere (crown) volgiamo salvare. Il carattere è in memoria e da adesso lo possiamo utilizzare come qualsiasi altro carattere (ricordi la procedure *putChar*?) e si fa nel seguente modo:
> *esempio: 
let crown = [0x00,0x11,0x15,0x15,0x1f,0x1b,0x1f,0x00]
lcd.customChar(0, crown) 
lcd.putChar(char(0))*

Ci cono poi altre procedure come *backLigtOff / backLigtOn* oppure *showCursor / hideCursor* che gai dal nome dovrebbe essere molto chiaro cosa fanno quindi su di esse non mi soffermo. Nell'indirizzo qui sotto, oltre alla libreria si possono trovare alcuni semplici file di esempio che riassumono quanto scritto sopra. Ora dovremmo avere tutto il necessario per divertirci con il nostro display.

<https://github.com/Martinix75/Raspberry_Pico/tree/main/Libs/display1602>

