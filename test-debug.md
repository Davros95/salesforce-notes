
# Test e debug <a href="#toc162445440" id="toc162445440"></a>

Si dichiarano con @isTest sui metodi e le classi che li contengono. Una classe di test è accessibile soltanto da un test; si definisce public se è una _test data factory_ cui devono accedere altre classi, altrimenti private.

Un metodo di test è sempre accessibile dal sistema di test e perciò non si definisce né public né private, tuttavia deve essere static.

Le classi di test non sono considerate nel calcolo dello spazio occupato dal codice Apex.

### Test e copertura <a href="#toc162445441" id="toc162445441"></a>

* Di default, ad ogni deploy in produzione girano sempre tutti i test _locali_ (proprio namespace, non managed packages)
* Si può scegliere di non farli girare, ma solo se non si stanno rilasciando classi o trigger
* Se si fanno girare i test _locali_ (più lento, meno severo):
* Un trigger deve avere una copertura minima dell’1%
* La copertura complessiva di codice _locale_ già presente ed in ingresso non deve mai scendere sotto al 75%
* Se si fanno girare test _specifici_, indicando le classi di test interessate (più veloce, più severo):\
  ogni trigger e classe del pacchetto devono essere coperti almeno al 75%
* In produzione si può accettare di rilasciare già attivi flow e processi qualora la copertura non sia minore della percentuale desiderata (anche 0%!), da _Process Automation Settings_

### Note <a href="#toc162445442" id="toc162445442"></a>

* Per ottenere la corretta percentuale di copertura test **globale** lanciare i test come **Test** | **Run All** anziché solo quelli effettivamente desiderati
* I metodi Test.startTest() e .stopTest() permettono di:
  * utilizzare governor limits nuovi per il codice di test incluso tra essi
  * ⚠️Eseguire le chiamate a metodi _asincroni_ in modo _sincrono_, fondamentale per i test!
* Si possono creare dei “pacchetti di test” creando una _Test Suite_
* Un metodo di test non può:
  * ⚠️accedere ai dati della org ad esclusione degli oggetti di sistema e dei metadati (es. profili, user, record type), a meno che non si specifichi @isTest(SeeAllData=true) (solo sul metodo e purché non ci sia un TestSetup)
  * inviare mail o eseguire callout a servizi esterni
  * utilizzare il linguaggio SOSL, che restituirà sempre risultati nulli a meno che non ne siano stati definiti di prova usando Test.setFixedSearchResults()
* Se più metodi creano gli stessi dati di prova, per risparmiare risorse creare un metodo per inizializzarli annotandolo con @testSetup (uno solo per classe, static void): verrà eseguito per primo ed i record creati verranno riportati allo stato originario prima dell’esecuzione di ogni metodo di test (per essere poi rimossi alla fine). L’alternativa è usare una classe di test “utility” a cui richiamarsi.
* Si possono inserire record di riferimento per le classi di test in una _static resource_ di tipo .csv e caricarli, con Test.loadData(_nomeOggetto_.sObjectType, _nomeStaticResource_),\
  in un “database ad hoc” accessibile dai test
* System.runAs(user){ codice… } può essere usato nei test per lanciare del codice come un utente qualsiasi, anche nuovo, anche se non si dispone della specifica licenza; considera solo le sharing rules ma non FLS / CRUD

#### Debug

Un _Debug level_ è una combinazione di differenti _Log level_ assegnati alle categorie di operazioni.

I livelli disponibili sono NONE, ERROR, WARN, INFO, DEBUG, FINE, FINER, FINEST e possono essere assegnati alle categorie:

* Apex Code
* Apex Profiling: limiti, numero di email inviate
* Callout: flusso con servizi esterni
* Database: DML, SOSL, SOQL
* System: anche System.debug()
* Validation
* Visualforce
* Workflow: comprende flow, processes
* NBA: Einstein Next Best Action

Una riga di log è strutturata come:

_timestamp|tipoOperazione|\[numero riga (se disponibile)]|dettagli specifici…_

L’header del log (visibile solo se viene scaricato) mostra versione dell’API e Debug level.

#### Trace Flags

Si abilitano da _Setup | Debug Logs_.

Sono dei marcatori temporanei che agiscono sulla quantità ed il dettaglio dei log che possono essere registrati. Possono essere applicate a:

* Automated Process (user di sistema per automazione)
* Platform Integration (user di sistema per Einstein e sincronizzazione dati)
* singoli utenti
* classi e trigger✳️

I log generati sono visibili nella stessa pagina, tranne che per classi e trigger: per essi sono soltanto modificati i livelli di dettaglio qualora venissero prodotti.

#### Log Inspector

* **Stack tree**
* **Execution tree**: gerarchia delle operazioni, con durata e spazio occupato
* **Performance tree**: elenco con valori aggregati e numero di iterazioni
* **Execution stack**\
  Scelta un’operazione, mostra quelle che l’hanno chiamata in gerarchia (dall’alto verso il basso)
* **Execution log**\
  Riprende il log testuale evidenziando le righe corrispondenti all’operazione selezionata.\
  Se si spunta **This Frame** la vista è ridotta alle operazioni allo stesso “scope” di quella selezionata
* **Source**\
  Scelta un’operazione, mostra codice e definizioni di metadato usate (se disponibili)
* **Variables**\
  Scelto un evento inerente variabili, mostra una tabella proprietà-valore
* **Execution overview**
* **Save order**: sequenza delle regole applicate ai record (trigger, workflow…), divise per oggetto
* **Limits**: consumo dei _governor limits_
* **Timeline**: sequenza e tempo impiegato da ogni tipo di operazione
* **Executed Units**: statistiche temporali, di limiti e di memoria consumata per ogni tipo di operazione (valori minimi, medi, percentuali…)

#### Checkpoints

Per scattare un’istantanea degli oggetti impiegati dal codice in azione. Per ciascuno di essi sono disponibili le viste:

* **Heap**
* **Types**: elenco di tutti i _tipi_ di oggetti che sono stati istanziati correntemente e spazio occupato
* **Instances**: scelto un oggetto, elenco di tutte le sue istanze e spazio occupato
* **State**: scelta un’istanza, tabella proprietà-valore
* **Symbols**\
  Vista di tutti i simboli con i loro valori e spazio occupato (più sintetica)
