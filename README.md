## BUBBLE SORT VISUALIZATION

Mostra il bubble sort operativamente: https://salvatorecapolupo.github.io/bubble-sort-visualization/

---

## 1. La Struttura (HTML)

L'HTML funge da impalcatura. Abbiamo bisogno di due aree principali:

1. Un'area dove "vivono" le barre (l'array).
2. Un'area per i comandi (bottoni e slider).

```html
<div id="array-container"></div>

<div class="controls">
    <button ...>Genera Array</button>
    <button ...>Ordina</button>
    <input type="range" ...> </div>

```

* **`id="array-container"`**: È fondamentale. Inizialmente è vuoto. Lo riempiremo dinamicanente con Javascript creando tanti `<div>` quante sono le barre.

---

## 2. Lo Stile e l'Orientamento (CSS)

Qui avviene la magia per rendere il grafico **orizzontale**. Usiamo **Flexbox**.

### Il Contenitore (Flexbox)

```css
#array-container {
    display: flex;             /* Mette gli elementi uno accanto all'altro */
    align-items: flex-end;     /* Allinea le barre in basso */
    justify-content: center;   /* Centra tutto nello schermo */
    height: 300px;             /* Altezza massima del grafico */
}

```

* **Perché `align-items: flex-end`?** Normalmente i div partono dall'alto. Noi vogliamo che sembrino un istogramma che parte da terra, quindi li allineiamo al fondo del contenitore.

### Le Barre

```css
.bar {
    background-color: #8a2be2; /* Colore base (viola) */
    width: 30px;               /* Larghezza fissa o % */
    margin: 0 2px;             /* Spazio tra le barre */
    transition: height 0.2s;   /* Rende fluido il cambio di altezza */
}

```

* Invece di cambiare posizione, cambieremo l'**altezza** (`height`) delle barre via JavaScript per rappresentare i numeri.

### I Colori di Stato

Definiamo classi specifiche per indicare cosa sta succedendo:

* `.comparing` (Rosso): Stiamo confrontando questi due elementi.
* `.sorted` (Verde): Questo elemento è nella sua posizione finale.

---

## 3. La Logica e l'Animazione (JavaScript)

Questa è la parte più complessa. Dobbiamo rallentare il computer, che altrimenti ordinerebbe l'array in microsecondi, rendendo l'animazione invisibile.

### A. Generazione dell'Array

```javascript
function generateArray() {
    container.innerHTML = ""; // Pulisce il vecchio array
    // Ciclo per creare 30 barre
    for (let i = 0; i < 30; i++) {
        let val = randomInt(20, 280); // Altezza casuale
        const bar = document.createElement("div"); // Crea un <div>
        bar.style.height = `${val}px`; // Assegna l'altezza
        bar.classList.add("bar"); // Assegna lo stile CSS
        container.appendChild(bar); // Inserisce nel foglio
    }
}

```

### B. Il "Motore del Tempo" (Async/Await)

Per creare l'animazione, usiamo una funzione `sleep` che restituisce una "Promessa" (Promise) che si risolve dopo un certo tempo.

```javascript
function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

```

Quando usiamo `await sleep(100)`, diciamo al codice: *"Fermati qui per 100 millisecondi, lascia che il browser disegni i colori rossi/verdi, e poi riprendi"*.

### C. L'Algoritmo Bubble Sort Animato

Ecco il flusso logico tradotto in codice all'interno della funzione `startBubbleSort()`:

1. **Doppio Ciclo:** Come nel classico Bubble Sort, abbiamo un ciclo esterno (`i`) e uno interno (`j`).
2. **Selezione Elementi:** Prendiamo le barre HTML attuali:
```javascript
const bars = document.getElementsByClassName("bar");

```


3. **Colorazione (Rosso):** Prima di confrontare, coloriamo di rosso barre `j` e `j+1`.
```javascript
bars[j].classList.add("comparing");
bars[j+1].classList.add("comparing");

```


4. **Pausa:** `await sleep(velocità)` -> Qui l'utente vede il rosso.
5. **Confronto e Scambio:**
* Leggiamo l'altezza (`parseInt(bars[j].style.height)`).
* Se la barra sinistra è più alta della destra, scambiamo le altezze.


```javascript
if (val1 > val2) {
    bars[j].style.height = `${val2}px`;   // Scambio visuale
    bars[j+1].style.height = `${val1}px`;
}

```


6. **Pulizia:** Rimuoviamo la classe `.comparing` (tornano viola).
7. **Conferma (Verde):** Alla fine del ciclo interno, l'elemento più grande è sicuramente arrivato in fondo (è "emereso" come una bolla). Lo coloriamo di verde.
```javascript
bars[array.length - i - 1].classList.add("sorted");

```



### Riepilogo del Flusso Visivo

1. **Javascript:** "Colora barra 1 e 2 di rosso".
2. **Browser:** Mostra barre rosse.
3. **Javascript:** `await sleep` (Pausa).
4. **Javascript:** "Barra 1 è più alta? Sì. Inverti le altezze".
5. **Browser:** Aggiorna il disegno con le altezze invertite.
6. **Javascript:** "Rimuovi rosso".
7. **Ripeti.**

In questo modo, manipolando il DOM (gli elementi HTML) passo dopo passo con delle pause in mezzo, creiamo l'illusione di un'animazione fluida.
