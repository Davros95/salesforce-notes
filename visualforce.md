
# Visualforce <a href="#toc162445453" id="toc162445453"></a>

Nel paradigma Model-View-Controller, essa corrisponde alla _view_; il _controller_ è la logica che interagisce tra essa ed il _model_, cioè il database. Le pagine Visualforce possono essere usate:

* Da sole, se è stata creata ed associata una _tab_ con la visibilità desiderata&#x20;
* Come componente in una Lightning Record Page, se è stata abilitata spuntando _Available for Lightning Experience, Lightning Communities, and the mobile app_&#x20;
* Come pagina aperta da quick action, anche standard: nelle opzioni dei singoli oggetti esse possono essere modificate in modo da puntarvi&#x20;

Ad ogni interazione la pagina viene ricalcolata, e tutti i valori che devono essere conservati e scambiati tra utente e server vengono immagazzinati nel _View State_ a meno che una variabile (non standard) non sia stata definita come _transient_ a livello di codice, caso in cui viene sbiancata.

### Controller <a href="#toc162445454" id="toc162445454"></a>

* **Standard (Record) Controller**\
  È quello predefinito: ogni oggetto ne ha uno (anche i custom), con il suo stesso API Name. Reagisce all’Id del record passatogli nella URL come ? …\&id=… esponendone i campi alla pagina; ha programmate azioni di manipolazione che avranno corrispondenti bottoni e link.
* **Standard List Controller**\
  Permette di lavorare con liste di record di sObject. Alcune proprietà ed azioni tipiche dell’impaginazione delle liste sono HasPrevious/Next (Boolean), e Previous/Next/First/Last.⚠️Non permette di ordinare i risultati!
* **Custom Controller**\
  Alternativo allo standard, è il nome di una classe Apex scritta dall’utente. È obbligatorio usarlo se si sta realizzando un _Componente Visualforce_. Non deve per forza avere un costruttore (⚠️se sì, senza parametri in ingresso), ma metodi o proprietà _getter_ per i valori a cui si deve accedere
* **Custom List Controller**\
  Se la versione standard non è sufficiente agli scopi. Nel codice deve essere istanziata la classe ApexPages.StandardSetController a cui vanno passati i record come lista o risultato di query, ovvero Database.getQueryLocator(query…)
* **Estensioni**\
  I controller possono essere estesi con classi personalizzate.\
  ⚠️Il collegamento logico non avviene con il termine extends nella classe: il loro costruttore deve avere come parametro il tipo ApexPages.StandardController (che permette di usare subito i metodi getId() e getRecord() ) oppure una classe controller personalizzata

### Componenti  <a href="#toc162445455" id="toc162445455"></a>

* {! espressione }\
  Si può usare nel testo normale, ma anche per valori di attributi.\
  Può contenere variabili primitive od oggetti, funzioni, espressioni con operatori, chiamate a metodi solo per lanciare azioni (stesso nome) oppure per accedere a quelli “getter” secondo la corrispondenza {! expr } ⬄ getExpr()
* [Variabili globali](https://developer.salesforce.com/docs/atlas.en-us.224.0.pages.meta/pages/pages\_variables\_global.htm?\_ga=2.52332201.1462165938.1701080731-1875400034.1696324836), es. $User.FirstName, $Organization, $Action, $Resource
* [Funzioni varie](https://developer.salesforce.com/docs/atlas.en-us.pages.meta/pages/pages\_variables\_functions.htm?\_ga=2.52332201.1462165938.1701080731-1875400034.1696324836) come quelle disponibili nei campi formula
* [Operatori](https://developer.salesforce.com/docs/atlas.en-us.pages.meta/pages/pages\_variables\_operators.htm?\_ga=2.38628771.1462165938.1701080731-1875400034.1696324836): es. concatenazione: &
* \<apex:page>
* Se si usa un **controller standard**:
* standardController: lo stesso API Name dell’oggetto di lavoro
* recordSetVar =_nomeVar_: se presente, indica di lavorare con una **lista** di record; _nomeVar_ andrà richiamato nei componenti di interazione
* Se se ne usa uno **custom** si indica controller=nomeClasse e comunque non si specifica recordSetVar, si useranno i var nei componenti per iterazione
* extensions: lista di classi con cui estendere il controller (anche standard); in caso di conflitti, vince la prima versione trovata partendo da sinistra
* sidebar (Boolean): vale solo per Salesforce Classic&#x20;
* showHeader (Boolean): vale solo per Salesforce Classic&#x20;
* standardStylesheets (Boolean), lightningStylesheets (Boolean)
* renderAs = “pdf”
* \<apex:component>\
  Per creare componenti personalizzati che accettano in ingresso dei parametri e restituiscono dei valori complessi. I parametri si definiscono con\
  \<apex:attribute name=”varName” type=”…” description=”…”>\
  ed il componente sarà chiamato con \<c:nomeComponente varName=”…”>
* \<apex:detail>\
  Layout veloce dei valori di un record
* subject (String): l’id del record in questione
* relatedList (Boolean): per mostrarle o sopprimerle tutte
* \<apex:relatedList list="…"/>: riaggiunge quella desiderata (una per elemento)
* \<apex:pageBlock> con \<apex:pageBlockSection> e\<apex:pageBlockTable>\
  Per layout e tabelle personalizzati, con stile di base
* \<apex:outputFieldvalue=”expr”>\
  Restituisce il valore del campo richiesto con label, come se in un layout. Fuori da questi blocchi, solo il valore
* \<apex:pageBlockTable value=”lista” var=”nomeVariabile”>\
  Componente di iterazione. Per ogni elemento della lista, referenziato come indicato in var, costruisce righe inserendone i valori come richiesto da \<apex:column value=”expr”>
* \<apex:pageMessages/>: indica dove far apparire eventuali messaggi di errore
* \<apex:dataTable>, \<apex:dataList>, \<apex:repeat>\
  Componenti di iterazione per creare tabelle, liste, markup generico senza alcuno stile di partenza
* \<apex:outputLink value=”expr”>
* Se l’espressione **non** inizia con / al risultato verrà premesso apex/ ; questo non è desiderato se si vuole aprire un record indicandone l’Id
* Spesso si usa la funzione URLFOR($Action.Oggetto.NomeAzione, IdOggetto) per inserire azioni e bottoni dove non previsti normalmente
* \<apex:outputText value=”expr” escape=”true | false”>\
  Per mostrare il testo contenuto in value; i caratteri sensibili sono resi in modo sicuro a meno che non sia stato specificato escape=”false” (esponendo al rischio di XSS)
* \<apex:form>\
  Per visualizzare _e modificare_ i dati, quindi rispedirli verso il database
* \<apex:inputField value=”expr”>: pre-valorizzato se è stato fornito un Id di record, considera visibilità del campo, se è required…\
  ⚠️E’ mostrato come in un layout _solo se è dentro un_ \<apex:pageBlockSection>, così come \<apex:outputField>
* \<apex:input… | :select… >: vari elementi utili per i form
* \<apex:actionSupport>: combinazione tra evento di interazione ed azione corrispondente
* event: onChange, …
* reRender (Id): quale elemento della pagina aggiornare (anche reinviando le informazioni al server)
* \<apex:commandButton>, \<apex:commandLink>
* action=”{! metodo }”: l’azione chiamata è un metodo del controller (cfr. URLFOR())
* value: testo da mostrare
* \<apex:stylesheet value = … >, \<apex:includeScript value = … > permettono di aggiungere file CSS e JS come static resources (raccomandato), ma anche direttamente come URL
* \<script>: per richiamare funzioni dallo script JS già incluso, o definirle sul posto
* \<apex:iframe>: per includere pagine complesse
* src: specifica l’indirizzo; per maggior isolamento, usare {!$IFrameResource._nome_} dove il _nome_ è quello della pagina salvata come _static resource_
* \<apex:includeLightning>: per includere Aura (ed LWC), ad inizio pagina.\
  Va abbinato ad uno script con

$Lightning.use(“c: | _packageNamespace_: _nome_, function() {

$Lightning.createComponent(_nome_, _attributi_, _posizioneDOM_, _callback_)

})

* ⚠️ Il componente Aura deve estendere la libreria _lightningOut_ e riportare le proprie dipendenze
* **Attributi generali**
* rendered (Boolean): se è vero, l’elemento è disegnato nella pagina, altrimenti no

### Apex per Visualforce <a href="#toc162445456" id="toc162445456"></a>

* [**System.ApexPages**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_methods\_system\_apexpages.htm)\
  Per inviare messaggi alla pagina e ricavarne delle informazioni generali
* .addMessage(ApexMessage); .hasMessages(), .getMessages()
* .currentPage() 🡪 PageReference
*
* **ApexPages.Message**\
  Per istanziarne uno si specificano:
* severity = CONFIRM | ERROR | FATAL | INFO | WARNING
* summary
* facoltativamente detail, ed id del componente a cui legare il messaggio
* [**System.PageReference**](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_system\_pagereference.htm)\
  L’oggetto con cui è rappresentata la pagina Visualforce nel codice.\
  Si può istanziare con new PageReference(urlParziale) o riferirvisi in modo “solido” come Page.nomePagina (preferibile perché il sistema riconosce la dipendenza del codice da essa).\
  Metodi utili sono:
* .getUrl(): restituisce il percorso relativo
* .getParameters(): una mappa dei parametri passati alla pagina via query, dopo “?”\
  Si usa per passare le informazioni del record ad un custom controller
* **ApexPages.StandardController**\
  Vi si fa riferimento in estensioni e custom controller per farli dialogare con la pagina ed il database.
* ⚠️Per le estensioni, deve essere inserito come unico parametro nel costruttore.
* .edit(), .save(), .delete(), .cancel(): azioni tipiche
* .getId(), .getRecord(): per ottenere l’Id o l’sObject con i dati del record corrente (limitandosi ai campi nella pagina)
* **ApexPages.StandardSetController**\
  Per lavorare con liste di oggetti. Fornisce il _prototipo_ dell’sObject che, se valorizzato in qualche campo, estenderà quei valori a tutti i record di lavoro in fase di salvataggio (per update massivi).
* ⚠️deve essere istanziato nel controller per funzionare, con la lista di record oppure con la query che li restituisce (come Database.getQueryLocator(\[query]) ).
* .first(), .last(), .previous(), .next();.getHasPrevious() | Next(); .get | setPageNumber(n), .get | setPageSize(n)\
  per la navigazione e l’impaginazione dei risultati
* .get | setSelected(list): per ottenere o impostare i record “selezionati” nella lista
* .getResultSize(): dà il numero totale di record di lavoro
* .getRecord(): dà il record prototipo modificabile come sopra
* .getRecords(): restituisce la lista di record nella pagina corrente (sola lettura)
* .getCompleteResult() 🡪 Boolean: è true se non si è raggiunto il limite di 10k record

### Sicurezza <a href="#toc162445457" id="toc162445457"></a>

* Gli _standard controller_ e le loro estensioni girano sempre nel contesto dell’utente, mentre i metodi dei _custom_ girano nel contesto di sistema; possono essere esplicitamente dichiarati with sharing per considerare le regole di accesso ai record
* CRUD e FLS sono sempre ignorate
* Non è necessario rendere esplicitamente visibile all’utente una classe usata nella pagina, essa funzionerà comunque
* Valgono le considerazioni fatte per le classi Apex

### Note  <a href="#toc162445458" id="toc162445458"></a>

1. ⚠️Le variabili statiche non vengono considerate dalle Visualforce!
2. Dalle impostazioni utente si può attivare la _Development Mode_ che permette di modificare in tempo reale la pagina selezionata, dividendo lo schermo a metà; allo stesso modo si può abilitare l’accesso al _View State_
3. ⚠️_Bisogna usare_ il controller standard (eventualmente esteso) affinché una pagina possa essere:
4. inclusa in un layout
5. lanciata da un Custom Button
6. o da uno Standard di cui si è fatto l’override
7. ⚠️_Non si può usare_ il controller standard per:
8. Modificare valori del record padre
9. Eseguire callout
10. Inserire un allegato ad un record