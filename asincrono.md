# Asincrono <a href="#toc162445382" id="toc162445382"></a>

I test devono essere eseguiti in modo sincrono!\
Racchiudere le chiamate ai metodi tra Test.startTest() e Test.stopTest()

## Batch Apex <a href="#toc162445383" id="toc162445383"></a>

Per operazioni sui record che non rispetterebbero i normali governor limits (es. pulizia ed archiviazione, fino a 50M di record): i record vengono processati in blocchi (l’ordine non è prestabilito) ed i limiti ripristinati ogni volta. Il fallimento di un batch non pregiudica l’esito degli altri.

### Definizione classe

**Attenzione ai tre metodi caratteristici!**

public class MyBatchClass implements Database.Batchable\<sObjectLetterale!> {

_\[eventuali variabili di istanza, vedi Database.Stateful]_

public Database.QueryLocator | Iterable start(Database.BatchableContext bc) {

_operazioni di raccolta record…_

return Database.getQueryLocator(query per ottenere i record) | Iterable…;

}

public void execute(Database.BatchableContext bc, List<_oggetto_> records){

_processo sui record…_

}

public void finish(Database.BatchableContext bc){

_operazioni da eseguire al termine della totalità dei batch…_

}

}

### Chiamata nel codice

MyBatchClass oggettoBatch = new MyBatchClass();

Id batchId = Database.executeBatch(oggettoBatch, _\[num. record/batch]_);

### Note

1. In fase di test si può eseguire soltanto un batch, attenzione a non eccedere il numero di record stabilito
2. Il numero di record per batch predefinito è di 200
3. L’ID assegnato sopra è di un record di tipo _AsyncApexJob_
4. Se si eseguiranno callout implementare _Database.AllowsCallouts_
5. Se serve tracciare il totale di record lavorati con una variabile di istanza deve essere implementata anche la proprietà _Database.Stateful_
6. Si fa restituire un _Iterable_ al metodo _start_ se i record devono essere analizzati e pre-processati in sequenza (in questo caso si torna ai normali governor limits)

## Future Methods <a href="#toc162445384" id="toc162445384"></a>

La loro definizione è molto semplice e possono essere facilmente impiegati, solitamente per callout a risorse esterne o quando devono essere eseguiti calcoli molto pesanti. L’ordine di esecuzione non è garantito, alcuni potrebbero avvenire anche in contemporanea.

### Definizione classe

Basta premettere @future al metodo in questione, che tuttavia:

* Deve essere static void
* In input accetta solo tipi primitivi, perciò in caso di oggetti (che potrebbero essere cambiati nel tempo tra la chiamata e l’esecuzione) si devono usare i loro ID

Se si eseguiranno callout premettere @future(callout=true)

### Note

1. Massimo 50 chiamate a future methods per invocazione Apex
2. I governor limits sono più ampi
3. Un future method non può chiamarne un altro ricorsivamente, neanche passando per un trigger intermedio (cfr. _Queuable Apex_)
4. Non hanno associati Apex job ID e non possono essere monitorati
5. In fase di test non si possono eseguire dei callout, se il metodo li prevede dovranno essere simulati
6. Raccogliere più callout possibili all’interno di uno stesso future method, anziché frammentarli
7. Possono racchiudere una chiamata a metodi sincroni in modo tale da renderli asincroni facilmente

## Queuable Apex <a href="#toc162445385" id="toc162445385"></a>

Al contrario dei future methods possono lavorare con oggetti complessi e non solo i tipi primitivi; si possono chiamare a catena (un solo genitore, un solo figlio); sono monitorabili.

### Definizione classe

public class SomeClass implements Queueable {

…

public void execute(QueueableContext context) {

…

}

}

### Chiamata nel codice

MyQueueableClass oggettoQueueable = new MyQueueableClass(_\[parametri..]_);

Id jobId = System.enqueueJob(oggettoQueueable, \[asyncOptions]);

_Vedi asyncOptions nel codice notevole_

### Note

1. Se si eseguiranno callout implementare Database.AllowsCallouts
2. In fase di test non si possono chiamare job secondari, pertanto se sono necessari si dovrà premettere loro la condizione if( not(Test.isRunningTest()) )
3. In una sola transazione si possono inserire fino a 50 job queuable
4. Solo per Developer Edition e org in prova, si possono concatenare fino a 5 livelli di job; altrimenti, non ci sono limiti

## Schedulable Apex <a href="#toc162445386" id="toc162445386"></a>

In questo modo i processi vengono lanciati in momenti prestabiliti, con una certa cadenza. Spesso si utilizzano per eseguire operazioni in batch.

### Definizione classe

public class SomeClass implements Schedulable {

…

public void execute(SchedulableContext ctx) {

…

}

}

### Chiamata nel codice

MySchedulableClass oggettoSchedulable = new MySchedulableClass();\
String CRON = ‘sec min ore ggMese mese ggSett \[anno]’;\
String⚠ jobID = System.schedule(nomePersonalizzato, CRON, oggettoSchedulable);

* Viene considerata la zona oraria dell’utente, ma il contesto è quello del sistema, con tutti i permessi
* Per la formattazione della stringa CRON vedere _Apex Scheduler_ nella documentazione
* La calendarizzazione può essere fatta anche da _Setup > Apex Classes > Schedule Apex_

### Note

1. Si possono schedulare al massimo 100 job.
2. Per informazioni sui job schedulati studiare gli oggetti _CronTrigger_ e _CronJobDetail_ nella documentazione
3. Se si eseguiranno callout implementare _Database.AllowsCallouts_; quelli sincroni a servizi esterni non possono essere eseguiti, bisogna passare per dei _future methods_
4. Per testare il codice inserire una espressione CRON qualsiasi e possibilmente una verifica che non ci siano job già programmati; il sistema lancerà il job in modo sincrono ignorando l’espressione
5. Attenzione a schedulare da un trigger per non superare i governor limits specifici!
6. Non si possono eseguire callout **sincroni** da codice schedulato

## Note generali <a href="#toc162445387" id="toc162445387"></a>

1. L’istanziamento della classe si può fare anche direttamente nel metodo di chiamata come\
   _new nomeClasse()_
2. Monitoraggio:
3. Per controllare lo stato dei job passati e futuri cercare _Apex Jobs;_ per vedere i soli batch c’è un link in quella pagina
4. Per controllare lo stato della coda e modificare l’ordine di esecuzione dei job in attesa cercare _Apex Flex Queue_, che però non mostra i Future Jobs (tornare su Apex Jobs)
5. Una query SOQL tipo per ricavare informazioni su job è\
   AsyncApexJob jobInfo = \[SELECT Status, NumberOfErrors FROM AsyncApexJob WHERE Id = :jobID];