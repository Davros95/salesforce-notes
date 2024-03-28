# 👍Data model <a href="#toc162445402" id="toc162445402"></a>

### Relazioni tra oggetti <a href="#ole_link58" id="ole_link58"></a>

#### Relazioni Master-Detail

* **Sharing Settings:** le impostazioni _Org-Wide Default_ del figlio perdono di significato.\
  La visibilità e/o editabilità del record figlio è “ereditata” dal padre come stabilito nelle impostazioni del campo relazione: il **minimo accesso** sul record padre per avere _Read/Write_ sul figlio può essere _Read Only_ oppure _Read/Write_ stesso
* **Campo Owner:** quello del figlio non è più accessibile; nei fatti il valore coincide con quello del record padre
* **Layout:** il campo relazione diventa necessariamente visibile, in quanto _required_
* **Queues:** un record figlio non può esservi inserito (servirebbe il campo _Owner_)
* **Roll-up Summary Fields:** su valori numerici, date, %, currency anche in campi formula, purché non ricavati da altri record attraverso relazioni (_cross-object_)
* **Multi-Currency:** prevale quella del record padre, impiegata nei Roll-up Summary Fields e in cui vengono convertite (affiancandole) quelle dei figli se diverse
* Alcuni oggetti standard non possono essere scelti come master, tra cui: _User, Lead, Product2, Pricebook2_

#### Oggetti esterni e Relazioni lookup <a href="#ole_link57" id="ole_link57"></a>

Una volta che è stata definita una sorgente ­di dati esterna (v. _Salesforce Connect_) è possibile rappresentare le informazioni in oggetti Salesforce definendo degli _External Objects_ e mappando i field agli _External column names_. Qui sono disponibili particolari relazioni lookup:

* **External lookup:** da un figlio in Salesforce ad un padre esterno, di cui si è definito l’External Id
* **Lookup:** da un figlio esterno ad un padre in Salesforce, il cui Id è presente tra i dati del figlio
* **Indirect lookup:** da un figlio esterno ad un padre in Salesforce, usando un univoco External Id

#### Note

* Alcune relazioni standard sono formalmente _lookup_ ma hanno delle caratteristiche delle _master-detail_, per esempio Opportunity - Opportunity Product, Account - Opportunity, Campaign -Campaign Member permettono di usarei_Roll-up Summary Fields_
* L’oggetto _User_ presenta solo la speciale relazione _Hierarchical_ che permette di usare i _Roll-up Summary Fields_
* In caso di cancellazione di un record padre:
* per una _lookup_ si è stabilito nelle impostazioni se sbiancare il campo corrispondente dei figli o riassegnarli ad altro record; una eventuale undelete ripristina solo i campi non riassegnati
* per una _master-detail_, i figli vengono cancellati ma non sono visibili nel cestino, né ripristinabili singolarmente; la undelete del padre li ripristina assieme alla relazione
* se un figlio secondo una _master-detail_ era già stato cancellato prima del padre, non è più recuperabile
* ⚠️ Non tutti gli oggetti standard permettono il recupero automatico del legame tra padri e figli
* Un campo non può essere rimosso, cambiato di tipo o rinominato se vi sono riferimenti:
* nei campi formula e nel codice, flow, processi;
* nelle pagine Visualforce;
* nelle Sharing Rules
* Non è un problema per il SOQL dinamico perché è una stringa.
* I campi formula possono mostrare anche valori di record non accessibili all’utente, passando per una relazione
