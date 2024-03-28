# Servizi di Integrazione <a href="#toc162445423" id="toc162445423"></a>

Ogni sito verso cui si eseguono callout deve essere stato prima inserito nella lista dei siti approvati attraverso la pagina _Remote Site Settings_!\
Verranno autorizzate tutte le directory contenute in quella esplicitata\
(tutto ciò che “segue” l’indirizzo esplicitato)

### Callout HTTP <a href="#ole_link176" id="ole_link176"></a>

Impiegano REST con JSON (o XML, ma è meno usato). Sono i più facili da scrivere ed interpretare, ed i metodi più comuni sono:

* **GET:** ottenere dati (in maniera secca, indicando direttamente la sorgente)
* **POST:** creare dati (o per ottenerne ma dietro invio di dati dal richiedente)
* **DELETE:** cancellare dati
* **PUT:** creare o rimpiazzare dati (come una UPSERT)

**⚠️ Ricorda che il JSON è case sensitive!**

#### Get <a href="#ole_link24" id="ole_link24"></a>

HttpRequest request = new HttpRequest();

request.setEndpoint(… sito cui inviare la richiesta …);

request.setMethod('GET');

Http http = new Http();

HttpResponse response = http.send(request);

response.getStatusCode();

response.getBody();

Post

HttpRequest request = new HttpRequest();

request.setEndpoint(… sito cui inviare la richiesta …);

request.setMethod('POST');

request.setHeader('Content-Type', 'application/json;charset=UTF-8');

request.setBody(… compatibile con il tipo sopra indicato …);

Http http = new Http();

HttpResponse response = http.send(request);

response.getStatusCode();

response.getBody();

⚠️Usare metodi come JSON.deserializeUntyped (che necessita di un cast forzato, di solito in mappe e liste) e simili per trattare correttamente la risposta.

#### Test

I metodi di test non possono eseguire callout e si deve ricorrere a dei _mock._ Queste simulazioni vengono inviate alla classe al posto della risposta dell’endpoint normalmente chiamato eseguendo, subito prima della chiamata, il metodo Test.setMock(HttpCalloutMock.class, _\*_) dove al posto di \* si specifica:

* un oggetto _StaticResourceCalloutMock_ (che preleva una static resource)
* oppure uno custom definito in una classe di test a parte, dove si implementa l’interfaccia _HttpCalloutMock_.

Con una static resource

1. Creare una static resource in cui inserire il body della simulazione di risposta dell’endpoint (inizialmente tipo text/plain, poi diventerà JSON) e popolare una istanza di StaticResourceCalloutMock:

StaticResourceCalloutMock mock = new StaticResourceCalloutMock();

mock.setStaticResource(… nome risorsa …);

mock.setStatusCode(200);

mock.setHeader('Content-Type', 'application/json;charset=UTF-8');\


1. Usare il metodo setMock per far sì che il sistema risponda al callout con la risposta simulata:\
   Test.setMock(HttpCalloutMock.class, mock);\


Implementando l’interfaccia HttpCalloutMock a parte

1. Creare una classe di test di appoggio dove inserire le specifiche della risposta

@isTest

global class RispostaMock implementsHttpCalloutMock {

global HTTPResponse respond(HTTPRequest request) {

HttpResponse response = new HttpResponse();

response.setHeader('Content-Type', 'application/json');

response.setBody(_..contenuto in JSON.._);

response.setStatusCode(200);

return response;

}

}

1. Richiamare la risposta nella classe di test principale:\
   Test.setMock(HttpCalloutMock.class, new RispostaMock());

### Callout SOAP <a href="#toc162445425" id="toc162445425"></a>

Impiegano XML e WSDL (Web Services Description Language). Per convertire un file WSDL in classe Apex si può provare a farlo automaticamente da _Org Setup > Apex Classes > Generate from WSDL_. Se tutto va bene vengono prodotte contemporaneamente sia una classe per le chiamate sincrone che una per le asincrone.

#### Test

Nel caso SOAP, la creazione di mock di test avviene creando una classe di test di appoggio che implementa l’interfaccia _WebServiceMock_, dove viene definita la risposta simulata:

@isTest

global class RispostaMock implements WebServiceMock {

global void doInvoke(

Object stub,

Object request,

Map\<String, Object> response,

String endpoint,

String soapAction,

String requestName,

String responseNS,

String responseName,

String responseType) {

_impostazione della risposta cercando il metodo predisposto a darne per ciò che va testato…_

classeWSDL.metodoRisposta response\_x = new classeWSDL.metodoRisposta();

response\_x.return\_x = _..risposta..;_

_inserimento nella mappa di risposta…_

response.put('response\_x', response\_x);

}

}

Dopodiché si istanzia la classe di appoggio all’interno del metodo _Test.SetMock_ che stavolta si definisce così:

Test.setMock(WebServiceMock.class, new RispostaMock());

### Apex come Servizio Web REST <a href="#toc162445426" id="toc162445426"></a>

In questo caso si deve predisporre Apex perché la org possa essere interrogata da un _REST client_, come lo strumento a riga di comando _cURL_ oppure quello di Workbench, accessibile da\
_Workbench > utilities > REST Explorer._ La classe ed i metodi devono essere global.

Il contenuto delle richieste può essere in XML o in JSON, caso più semplice in cui l’header riporta le informazioni:

Content-Type: application/json; charset=UTF-8

Accept: application/json

#### Definizione della classe e dei metodi

@RestResource(urlMapping = … )

global with sharing class MyRestResource {

_@metodoHttp_

global static _..Tipo.._ metodo1() {

…

}

}

* L’endpoint corrispondente ad un servizio esposto è del tipo https://nomeOrg.my.salesforce.com/services/apexrest/… urlMapping , dove urlMapping attiva la classe corrispondente ed indica l’oggetto con cui si lavora, ad esempio /Cases/\* (l’asterisco corrisponde a “tutte le directory più interne”) o /Accounts/\*/Contacts (qualunque valore intermedio, sarebbe l’Id record)
* La classe deve essere per forza _global_ ed i metodi _global static_. Ad ognuno può essere associato un metodo HTTP, una sola volta, con l’annotazione corrispondente:

| @HttpGet | @HttpPost  | @HttpDelete | @HttpPut   | @HttpPatch        |
| -------- | ---------- | ----------- | ---------- | ----------------- |
| **read** | **create** | **delete**  | **upsert** | **update fields** |

Post

@HttpPost

global static ID createCase(..dichiarazione parametri input..) {

Case thisCase = new Case(..passaggio valori immessi ai campi..);

insert thisCase;

return thisCase.Id;

}

La richiesta va formulata con:

* Endpoint terminante con l’oggetto:

https://nomeOrg.my.salesforce.com/services/apexrest/Cases/

In Workbench si specifica solo /services/apexrest/Cases/

* Corpo: {“var1”:”val1”, “var2”:”val2”, … _non per forza nello stesso ordine_}

E viene restituito l’ID dell’oggetto creato.

Get

@HttpGet

global static Case getCaseById() {

RestRequest request = RestContext.request;

//Estrazione dell’ID dalla stringa ricevuta

String caseId = request.requestURI.substring(

request.requestURI.lastIndexOf('/')+1 );

Case result = \[_..query con il :caseId dei campi di interesse.._];

return result;

}

La richiesta va formulata con l’endpoint terminante con l’Id del record:

https://nomeOrg.my.salesforce.com/services/apexrest/Cases/_..Id.._

(In Workbench si specifica solo /services/apexrest/Cases/)

E vengono restituiti, di default come JSON, tipo e posizione del record, oltre ai valori dei campi previsti.

Delete

È pressoché identico al metodo GET, anche nella formulazione della richiesta. Di default restituisce un messaggio di conferma eliminazione che riporta l’Id del record cancellato, tuttavia si può configurare il metodo perché ne dia uno personalizzato.

Put

Per aggiornare singoli campi vedere **PATCH**\
Questo metodo considera un campo non specificato come null\
e cancella il valore originale!

L’unica differenza con il metodo POST è che nel metodo Apex deve essere previsto il parametro per ricevere in input l’Id del record su cui fare _upsert_; se dal lato della richiesta questo non viene specificato perché si vuole inserire un record nuovo non si avranno errori, di fatto verrà eseguito un _insert_.

Patch

Solitamente il metodo viene implementato in modo da accettare un numero arbitrario di coppie\
variabile-valore in JSON anziché parametri predeterminati. Così facendo, i campi non specificati mantengono il loro valore.

@HttpPatch

global static ID updateCaseFields() {

RestRequest request = RestContext.request;

//Estrazione dell’ID dalla stringa ricevuta e query

String caseId = request.requestURI.substring(

request.requestURI.lastIndexOf('/')+1);

Case thisCase = \[SELECT Id FROM Case WHERE Id = :caseId];

// Cast in mappa del blocco di valori in JSON

Map\<String, Object> params =

(Map\<String, Object>)

JSON.deserializeUntyped(request.requestbody.tostring());

// scorrimento chiavi e assegnazione valori all’oggetto

for(String fieldName : params.keySet()) {

thisCase.put(fieldName, params.get(fieldName));

}

update thisCase;

return thisCase.Id;

}

**Notare il metodo **_**put**_** per oggetti che funziona come per le mappe!**

La richiesta va formulata con l’endpoint terminante con l’Id del record:

https://nomeOrg.my.salesforce.com/services/apexrest/Cases/_..Id.._

(In Workbench si specifica solo /services/apexrest/Cases/)

e nel body si specificano direttamente le coppie variabile-valore in JSON.

Di default viene restituito l’Id del record

#### Test

Le interrogazioni REST devono essere simulate, definendole con queste proprietà:

* RestRequest request = new RestRequest();
* request.requestUri _= ..indirizzo completo.._ + recordId;
* request.httpMethod = … ;
* request.params.put(_..coppia chiave,valore come in una mappa.._);
* Solo per i metodi **GET, DELETE,PATCH** dopo aver definito request assegnarla come segue:\
  RestContext.request = request;
* Solo per il metodo **PATCH** la dichiarazione complessiva è:

RestRequest request = new RestRequest();

request.requestUri = _..indirizzo completo.._ + recordId;

request.httpMethod = 'PATCH';

request.addHeader('Content-Type', 'application/json');

request.requestBody = Blob.valueOf(_..chiavi:valori in JSON.._);

RestContext.request = request;

Dopo aver definito l’interrogazione si può chiamare il metodo da testare all’interno di quello di test.

#### Note

* Apex REST supporta solo i tipi primitivi (tranne Object o Blob), sObject, loro liste o mappe (con chiavi String soltanto) e tipi personalizzati che contengano quanto sopra.
* I metodi non rispettano i permessi di visibilità assegnati, solo le _sharing rules_ quando il metodo è stato dichiarato _with sharing._

### Trattamento dei dati in ingresso <a href="#toc162445427" id="toc162445427"></a>

Come appaiono i dati via via che vengono trattati?

* r = response.getBody()\
  { "nomeOggetto" : {”var1”:”val1”, ”var2”:”val2”, …} }
* j = JSON.deserializeUntyped(r)\
  { nomeOggetto = {var1=val1, var2=val2, …} }
* Map\<String,Object> m = (Map\<String, Object>) j\
  Come sopra, ma con il significato _chiave = valore_. Notare che si è dovuto eseguire il cast e la corrispondenza di sintassi!

### Risorse <a href="#toc162445428" id="toc162445428"></a>

* [https://json2apex.herokuapp.com/](https://json2apex.herokuapp.com/)
* I callout possono essere eseguiti anche in modo asincrono (necessario se vengono eseguiti da un trigger). Nel caso si usi un future method però va specificato @future(callout=true)
* cURL:\
  [https://developer.salesforce.com/docs/atlas.en-s.224.0.api\_rest.meta/api\_rest/intro\_curl.htm](https://developer.salesforce.com/docs/atlas.en-s.224.0.api\_rest.meta/api\_rest/intro\_curl.htm)
