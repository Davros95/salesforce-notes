# [SOQL](https://developer.salesforce.com/docs/atlas.en-us.soql\_sosl.meta/soql\_sosl/sforce\_api\_calls\_soql.htm?\_ga=2.172806883.1390319515.1693192700-850010389.1693188698) <a href="#toc162445433" id="toc162445433"></a>

Per ricerche:

1. nel database (ricerca esatta)
2. mirate in campi noti, anche numero, data, checkbox
3. su un solo oggetto, eventualmente passando per oggetti correlati
4. per ottenere un piccolo numero di risultati
5. con funzioni di ordinamento

definizione

SELECT campo1, campo2, … (query record figli) FROM oggetto WHERE…

### Uso di SELECT <a href="#toc162445434" id="toc162445434"></a>

1. Si possono richiedere tutti i campi con FIELDS(ALL), ed esistono anche\
   FIELDS(STANDARD | CUSTOM); c’è bisogno di restringere i risultati con un WHERE o LIMIT ed il massimo numero di record scende a 200
2. Anche se scegli un solo campo restituisce l’oggetto

### Funzioni di Aggregazione <a href="#toc162445435" id="toc162445435"></a>

1. COUNT(…)

Note

Se impiegate, la query restituirà un oggetto di tipo AggregateResult{expr0=val0, expr1=val1, …}\
Per accedere ai suoi valori usare il metodo .get(expr\_n). Se si usano alias, si avranno essi al posto di expr\_n.

I valori aggregati contano ciascuno come singola riga ai fini dei _governor limits_

### Note <a href="#toc162445436" id="toc162445436"></a>

1. Massimo 100 operazioni per transazione per Apex sincrono, 200 per asincrono
2. Massimo 5 livelli di profondità nelle relazioni da figlio a padre, massimo 2 (cioè un passaggio) da padre a figlio per la query in Apex o sempre 5 in chiamate REST o SOAP
3. Massimo 50k record ottenibili in una transazione
4. Si possono usare _alias_ per le colonne, scrivendo il nome desiderato dopo quello del campo!
5. Le funzioni di aggregazione non possono essere usate nelle query innestate
6. WHERE IN (..lista, ..)
7. Per specificare combinazioni di valori in query di campi _multi-select picklist_ si scrive così:\
   ‘val1;val2’ ⬄val1 OR val2, ‘val1’,’val2’ ⬄ val1 AND val2 (⚠️attenzione agli apici!)
8. In un ciclo for iterativo si può utilizzare la query per popolare la lista degli elementi da scorrere:
   1. for(tipoElemento : \[ query ]) {…} (processa **un record** per volta)
   2. for(Lista\<tipoElemento> : \[ query ]) {…} (processa **un batch** per volta)\
      ⚠️ Questo secondo uso è fondamentale per ridurre il carico di memoria (_heap size_)
9. Le date non si scrivono tra apici, e devono essere in formato\
   YYYY-MM-DDThh:mm:ss…+hh:mm | -hh:mm | Z

* WITH SECURITY\_ENFORCED (tra WHERE e ORDER BY, LIMIT, OFFSET, Aggregate) : se l’utente non ha tutte le FLS/CRUD necessarie dà eccezione, meglio WITH USER\_MODE / SYSTEM\_MODE