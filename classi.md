# Classi <a href="#toc162445392" id="toc162445392"></a>

### Modificatori <a href="#toc162445393" id="toc162445393"></a>

#### Di accesso (accessModifier)

* private (default): accessibilità solo dal codice interno alla classe che contiene la variabile, il metodo, la classe interna
* protected: accessibilità dall’interno della classe e da quelle che la estendono
* public: accessibilità dall’interno del namespace e dell’applicazione
* global:accessibilità anche dall’esterno del namespace (es. per Web Service)
* Una classe interna può non presentare il modificatore (sarà private)
* ⚠️Una classe esterna può essere solo public o global; una di test anche private
* ⚠️Se un membro è global, anche la classe esterna deve esserlo

#### Di definizione (definitionModifier)

* virtual: la classe ammette estensioni ed override dei metodi dichiarati virtual a propria volta
* ⚠️Una classe può estenderne soltanto un’altra
* abstract: un metodo così dichiarato non è implementato; la classe è abstract se contiene almeno uno di questi metodi e dovrà essere estesa per poterli implementare
* static: il metodo o la variabile appartengono alla classe e non alle singole istanze, che ne condividono un’unica copia
* Solo le classi esterne possono avere membri statici
* Un metodo static non può essere protected
* ⚠️I membri statici non possono essere chiamati sulle singole istanze, ed i metodi statici non possono accedere ai valori propri delle istanze
* ⚠️Le variabili statiche possono essere aggiornate, ma conservano il loro valore solo nella transazione corrente
* static final: per una costante, il cui valore deve essere definito in concomitanza e non può più essere modificato
* transient: specifico per il codice di controller impiegati dalle pagine Visualforce, indica una variabile il cui valore deve essere escluso dal _viewState_ ovvero non ricalcolato ogni volta che la pagina viene aggiornata; utilizzabile anche nelle classi che implementano le interfacce _Batchable_ e _Schedulable_

#### Della valutazione delle Sharing Rules

* Le opzioni sono with sharing, without sharing, inherited sharing e applicabili solo alle classi, esterne ed interne
* Le classi interne **non ereditano** l’impostazione di quella esterna
* Le classi estese ed implementate **ereditano** l’impostazione della classe padre

### Annotazioni <a href="#ole_link1" id="ole_link1"></a>

* [@AuraEnabled](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_AuraEnabled.htm): rende il metodo accessibile ad Aura Components e LWC
* cacheable = true: permette di salvarne i risultati in cache\
  ⚠️ Il metodo sarà utilizzabile solo per ottenere dati, non per modificarne
* [@Deprecated](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_deprecated.htm): metodo, classe ecc. non possono più essere usate dal nuovo codice (continuano a funzionare)\

* [@Future](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_future.htm): vedi _Future Methods_
* callout = true: abilita ai callout
* [@InvocableMethod](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_InvocableMethod.htm): rende il metodo visibile a REST, Flow, Bot, Apex
* label, description danno informazioni
* callout = true abilita ai callout\
  (⚠️determinante se il flow deve decidere in autonomia come gestire le transazioni!)
* ⚠️incompatibile con altre annotazioni
* ⚠️il metodo dev’essere static e public / global; uno solo per classe
* [@InvocableVariable](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_InvocableVariable.htm)
* [@IsTest](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_isTest.htm): per classi esterne e metodi, public o private
* seeAllData = true: ignora qualsiasi restrizione sui dati; se posto true sulla classe, vale per tutti i metodi di test
* isParallel = true: tutte le classi così segnate possono far girare i test in parallelo (incompatibile con seeAllData)
* [@](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_testsetup.htm)TestSetup: il metodo verrà eseguito per primo ed i record creati verranno riportati allo stato originario prima dell’esecuzione di ogni metodo di test della classe, infine rimossi
* ⚠️ per un solo metodo per classe, static void; non utilizzabile se c’è almeno un seeAllData = true
* [@TestVisible](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_testvisible.htm): per rendere membri di classe, o classi interne, non pubblici accessibili ai soli metodi di test
* [@JsonAccess](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_JsonAccess.htm): stabilisce se le istanze della classe possono essere serializzate (convertite in JSON) o deserializzate (ricavate da un JSON), specificando:
* uno o entrambi tra i parametri serializable e deserializable
* e un valore tra never, sameNamespace, samePackage, always
* ⚠️Non è ereditato dalle classi che estendono
* [@NamespaceAccessible](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_NamespaceAccessible.htm)
* [@ReadOnly](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_ReadOnly.htm)
* [@RemoteAction](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_RemoteAction.htm)
* [@SuppressWarnings](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_annotation\_SuppressWarnings.htm)
* **annotazioni Apex REST**: vedi _Apex come Servizio Web REST_

### Note <a href="#toc162445395" id="toc162445395"></a>

1. [Riferimenti](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_classes\_understanding.htm?\_ga=2.229447260.1394455374.1698327966-1875400034.1696324836)
2. Una _interfaccia_ è l’astrazione completa di una classe, in cui tutti i metodi sono soltanto dichiarati ma non implementati, né presentano _access modifiers_. Una classe può implementare più interfacce ma solo se avrà implementato tutti i loro metodi
3. In una query si può inserire una variabile (anche lista) nella forma :nomeVariabile, direttamente, senza apici