

# Codice notevole <a href="#toc162445459" id="toc162445459"></a>

### 👍Proprietà e metodi _Getter/Setter_ <a href="#toc162445460" id="toc162445460"></a>

Le _proprietà_ sono a metà strada tra variabili e metodi, in quanto permettono di eseguire codice specifico quando si vuole ottenere (get) oppure impostare (set) un loro valore. Si dichiarano completi di modificatori e presentano un blocco “get” ed uno “set”, o uno solo dei due (saranno _read-only, write-only, read/write_); nel caso in cui non serva codice personalizzato, si scriverà:

modificatori… tipo nomeVar { get; set; }

In modo equivalente si possono scrivere i metodi _getter_ e _setter_.

Si legge ed imposta il loro valore “come al solito” (pur considerando la logica implementata al loro interno, e che essa si attiverà sempre, anche all’interno della stessa classe!)

| Proprietà                                                       | Metodo                                                   | Uso                                                |
| --------------------------------------------------------------- | -------------------------------------------------------- | -------------------------------------------------- |
| <p>public String var {</p><p>get {</p><p>return … ;</p><p>}</p> | <p>public String getVar() {</p><p>return … ;</p><p>}</p> | <p>System.debug(</p><p>nomeClasse.var</p><p>);</p> |
| <p>set {</p><p>var = value;</p><p>}</p><p>}</p>                 | <p>public void setVar(param…) {</p><p>…</p><p>}</p>      | nomeClasse.var = ‘a’;                              |

### 👍Descrizione del Data Model <a href="#toc162445461" id="toc162445461"></a>

#### Schema.[DescribeSObjectResult](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_methods\_system\_sobject\_describe.htm) <a href="#ole_link126" id="ole_link126"></a>

Si ottiene in uno di questi modi:

* Schema.describeSObjects(String\[] _tipiOggetto_) (per più oggetti)
* _nomeOggetto_.sObjectType.getDescribe()
* Schema.sObjectType._nomeOggetto_

ed i metodi principali sono:

* .getSObjectType() 🡪 String\
  Dà l’API Name dell’oggetto (attenzione!)
* getRecordTypeInfos() 🡪 List\<Schema.RecordTypeInfo>\
  getRecordTypeInfosBy… Id() || DeveloperName() | Name()\
  🡪 Map\<Id || String, Schema.RecordTypeInfo>\
  Elenca i RecordType associati, eventualmente mappandoli al loro Id, oppure API Name o label

#### Schema.[DescribeFieldResult](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_methods\_system\_fields\_describe.htm)

Si ottiene in uno di questi modi:

* _nomeOggetto_._nomeCampo_.getDescribe()
* Schema.SObjectType._nomeOggetto_.fields._nomeCampo_

ed i metodi principali sono:

* .getPicklistValues() 🡪 List\<Schema.PicklistEntry>\
  Per ottenere tutti i valori di una picklist
* .getLength() | .getDigits() | .getScale()\
  Restituiscono il massimo numero di caratteri | di cifre per un Integer | di decimali per un Double
* .isFormulaTreatNullNumberAsZero()\
  Vero se il campo è formula e questa è impostata per considerare un null, cioè un valore vuoto in Apex, come zero

#### Verifica permessi CRUD/FLS

Si possono chiamare i seguenti metodi delle classi\
Schema .DescribeSObjectResult | .DescribeFieldResult:

|       |                 | Schema.DescribeSObjectResult | Schema.DescribeFieldResult       |
| ----- | --------------- | ---------------------------- | -------------------------------- |
| **C** | .isCreateable() | Create su oggetto            | Create su oggetto, Edit su campo |
| **R** | .isAccessible() | Read su oggetto              | Read su oggetto e campo          |
| **U** | .isUpdateable() | Edit su oggetto              | Edit su oggetto e campo          |
| **D** | .isDeletable()  | Delete su oggetto            | -                                |

Questi invece sono i metodi della classe System.Schema_:_

* .describeSObjects(String\[] types) 🡪 DescribeSObjectResult\[]\
  Vedi sopra
* .getGlobalDescribe() 🡪 Map\<String, Schema.SObjectType>\
  Mappa da API Name a “token” di tutti gli oggetti di sistema
* .describeTabs() 🡪 Schema.describeTabSetResult\[]\
  Descrive le app presenti, permettendo di accedere ad informazioni quali descrizione, numero di tab…

#### Schema.RecordTypeInfo

1. .getName() | .getDeveloperName()\
   Restituiscono label ed API Name del RT

### 👍Operazioni con il Database <a href="#toc162445462" id="toc162445462"></a>

#### System.Database

Metodi per operazioni DML

I metodi si chiamano con lo stesso nome delle operazioni.

Si può ammettere successo parziale con il parametro allOrNone, per esempio:\
Database.operazione(listaRecord,allOrNone: true | false, \[AccessLevel])\
⚠ Questo significa che non vengono più lanciate eccezioni di sistema, ma si possono analizzare in dettaglio i risultati coni metodi .isSuccess(), .getId(), .getErrors() (vedi sotto).

Altri metodi notevoli

1. .query(String)

#### Classi Database._nomeOperazione_Result

I metodi per operazioni DML sopra citati restituiscono informazioni sull’esito operazioni per i singoli record, come lista di oggetti di classe Database.UpsertResult, Database.DeleteResult, e Database.SaveResult per le operazioni restanti.\
Questi oggetti possono essere ispezionati con i metodi:

1. .isSuccess() | .getId() | .getErrors()

### Altro <a href="#toc162445463" id="toc162445463"></a>

#### System.[Limits](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_methods\_system\_limits.htm)

Permette di consultare i _Governor Limits_ e verificare il loro stato di consumo nella transazione. La maggior parte dei metodi si può vedere come coppie:

* get_Tipo_()\
  per conoscere quante volte è stata eseguita una operazione
* getLimit_Tipo_()\
  per conoscere il valore del Governor Limit

Esempi di _Tipo_ sono: Queries, SoslQueries, DMLRows, DMLStatements, HeapSize, CpuTime

#### System.Exception ed estensioni

L’eccezione generica si estende principalmente nei tipi sotto, con alcuni motivi non banali:

* AuraException, AuraHandledException
* AssertException
* DmlException
* Se si esegue un’operazione senza avere impostati tutti i campi required
* FinalException
* Se si tenta di modificare un record in read-only (per esempio di un trigger _after update_) o una variabile final
* LimitException
* Se si supera un _governor limit_
* ListException
* Se si tenta di accedere a posti oltre i limiti della lista
* NullPointerException
* Se si tenta di lavorare con un valore null _(dereferenziare)_
* QueryException
* Se una query dà una lista di risultati, ma questi vengono assegnatiad un singolo oggetto
* Se non si hanno risultati, cioè una lista nulla, e si assegnano ad un singolo oggetto
* SObjectException
* Se si cerca di modificare il valore di un campo di sObject che non è stato incluso nella query
* eccezioni personalizzate, estensioni della generica Exception; possono essere classi esterne, presentare classi interne ed essere estese a propria volta

I metodi comuni sono:

* getCause: oggetto Exception interno che ha causato l’eccezione esterna (casi personalizzati)
* getLineNumber
* getMessage
* getStackTraceString
* getTypeName

Per sollevare un’eccezione (standard o custom, ⚠️esclusa la generica Exception) si esegue throw new nomeEccezione(…) con una stringa di errore e/o una _Exception_, oppure genericamente senza argomenti.

#### System.AsyncOptions

È per il codice _queueable,_ da passare come secondo parametro al metodo System.enqueueJob(). Le sue proprietà sono:

* MaximumQueueableStackDepth\
  Numero massimo di job chiamatisi ricorsivamente; sostituisce il valore di default di 5
* MinimumQueueableDelayInMinutes (0-10)\
  Tempo minimo che deve intercorrere tra due esecuzioni successive di una classe (per ricorsione o meno)
* DuplicateSignature (tipo QueueableDuplicateSignature)\
  Identificativo univoco del job, si usa per prevenire la sua duplicazione: il sistema solleva un’eccezione se se ne vuole inserire uno uguale nella coda.\
  Si costruisce con la classe System.QueueableDuplicateSignature.Builder, chiamando uno o più dei metodi addId(), addInteger(), addString() e poi finalizzando con build().

#### Altro ancora

* switch on _var_ { when val1, val2 {…} when val3 {…} … when else {} }
* **Safe navigation operator per oggetti**, a ?. b
* se ciò che è a sinistra NON è null, accede a ciò che è riportato a destra e valuta
* se ciò che è a sinistra È null, restituisce null (accedere a destra avrebbe dato errore)
* **Asserzioni**
* System.assert(cond) || .assertEquals | assertNotEquals(A,B)
* Assert.is | .isNot … instanceOfType | Null
* Assert.isTrue | .isFalse
* Assert.are | areNotEqual(A,B)
* Assert.fail()
* Per ognuno di questi metodi c’è un overload con eventuale messaggio di errore
* SObject.clone()\
  è un metodo per clonare istanze di oggetti. I suoi parametri, in sequenza, sono i booleani:
* preserveId: il valore Id deve essere copiato?
* isDeepClone: creare un’istanza nuova, o puntare alla prima?
* preserveReadonlyTimestamps
* preserveAutonumber
* Gli **Share Objects**\
  standard (es. AccountShare) e custom (es. _CustomObjName_\_\_Share) registrano la condivisione di record con utenti o gruppi. Contengono (rispettivamente):
* _ObjName_Id (specifico se _standard_) | ParentId (generico se _custom_):\
  Id del record da condividere
* UserOrGroupId: quello di un utente, o Public o Territory Group
* _StandardObjName_ShareAccessLevel | AccessLevel (come sopra):\
  Edit, Read; deve essere superiore a quello di default
* RowCause: manual, ???\
  modalità con cui è avvenuta la condivisione
* ⚠️Un oggetto custom figlio secondo una master-detail non ha uno share object!
* System.hashCode(…)
* Per **invocare un flow** _autolaunched_ da Apex (creare una _interview_) si deve istanziarlo con:
* Flow.Interview._nomeFlow_(_parametri_) (versione _statica_)
* Flow.Interview.createInterview(_nomeFlow_, _parametri_) (_dinamica_)
* passando i parametri come Map\<String, Object>, e infine lanciarlo con .start().
* Il valore di una sua variabile si ricava chiamando .getVariableValue(nome).