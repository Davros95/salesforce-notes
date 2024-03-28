# Eventi in Javascript <a href="#toc162445406" id="toc162445406"></a>

**Un evento si propaga tra gli elementi secondo tre fasi:**

* **Capturing: discende dal più esterno fino a quello che lo ha generato**
* **Targeting: lo raggiunge**
* **Bubbling: risale all’inverso fino a quello più esterno**

**Gli **_**event listener**_** possono essere associati ad un elemento con**

nomeElemento.addEventListener(type, listener, useCapture)

* type: tipo di evento per cui mettersi in ascolto
* listener: arrow function o nome funzione da eseguire
* useCapture: se l’event listener deve attivarsi in fase di _capturing_ o meno

**Un evento personalizzato si può definire con**

* new Event(type, \[options])\
  se a riferimento si vuole prendere un tipo standard a cui apportare qualche modifica (sconsigliato)
* new CustomEvent(type, \[options])\
  dichiarando un _tipo_ completamente nuovo.

options è un oggetto facoltativo che riporta:

* bubbles\
  Se deve risalire attraversando la gerarchia degli event listeners, o fermarsi al primo
* cancelable\
  Se l’evento può essere cancellato o meno (vedi preventDefault())
* composed\
  Se può “risalire” la gerarchia degli Shadow DOM dei componenti, o meno (fermarsi allo shadow boundary, cioè il nodo #shadow-root, del componente in cui è stato generato)\
  ⚠Questa “risalita” avviene solo lungo gli shadow DOM

_(Queste tre proprietà sono false di default per gli eventi generati da costruttore)_

* detail (solo per CustomEvent()): oggetto con dati notevoli da trasportare con l’evento

L’evento personalizzato si lancia con dispatchEvent(nomeEvento)

### Proprietà e metodi notevoli di Event <a href="#toc162445407" id="toc162445407"></a>

* .target\
  L'elemento nidificato più in profondità che ha lanciato l'evento**. Se nel suo percorso un evento attraversa uno **_**shadow boundary**_**, il suo valore si modifica nel componente che lo “inscatola” perché dall’esterno non si può più accedere all’elemento originario (**_**retargeting**_**)**
* .currentTarget\
  **L’elemento su cui è stato aggiunto l’event listener attivo al momento della valutazione**
* .composedPath()\
  Sequenza di elementi in cui sono presenti event listeners attivati dall’evento nel suo percorso
* .preventDefault()\
  Se è chiamato in un handler di un evento cancelable, impedisce il comportamento di default ma non ferma la propagazione
* .stopPropagation()\
  Ferma un’ulteriore propagazione dell’evento nella gerarchia, ma non ad eventuali event listeners per quello stesso tipo nello stesso elemento (“in orizzontale”)
* .stopImmediatePropagation()\
  **Ferma la propagazione anche “in orizzontale”**