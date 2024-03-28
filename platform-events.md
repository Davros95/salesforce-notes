# 👍Platform Events <a href="#toc162445419" id="toc162445419"></a>

Simili ad un oggetto Salesforce, campi compresi. I _messaggi_ sono le sue istanze ed i permessi disponibili sono soltanto read e create. Il ciclo di vita è questo:

1. Un _event producer_ genera un messaggio
2. Esso viene incanalato in un _event bus_ condiviso con altre istanze Salesforce (generalizzazione di un _event channel_) e conservati per 72 ore (per la generazione attuale, ovvero _high-volume_)
3. Gli _event consumers_ sottoscritti ed autorizzati possono accedere al bus e sincronizzarsi con gli eventi per conto proprio, scaricando tutti quelli ancora conservati oppure tutti a partire da un certo messaggio specificando il suo _ReplayId_

Le proprietà principali di un platform event sono:

* L’API Name termina in \_\_e
* Sono disponibili campi di tipo testo, numero, data/ora, checkbox
* Publish behavior:
* _Publish After Commit_\
  Il messaggio viene pubblicato soltanto al termine della transazione, se è avvenuta con successo. Preferibile per mantenere la **consistenza** dei dati.\
  Una esecuzione del metodo conta come _Apex DML statement_
* _Publish Immediately_\
  Il messaggio viene pubblicato subito, indipendentemente dall’esito della transazione, ed eventualmente prima che sia terminata. Utile principalmente per i **log.**\
  Una esecuzione del metodo conta come chiamata di EventBus.publish()

### Modalità di pubblicazione <a href="#ole_link36" id="ole_link36"></a>

#### Apex

Si utilizza il metodo EventBus.publish(event | eventList), che restituisce una o più istanze Database.SaveResult da poter esaminare.\
⚠️Questo metodo non lancia eccezioni né rollback in caso di errore

List\<EventMsg\_\_e>evList = new List\<EventMsg\_\_e> ();

// creazione dei singoli messaggi ed aggiunta alla lista…

List\<Database.SaveResult> srList =EventBus.publish(evList);

for (Database.SaveResult sr : srList) {

if (sr.isSuccess()) {

System.debug('Successfully published event.');

} else {

for(Database.Error err : sr.getErrors()) {

System.debug( err.getStatusCode() + err.getMessage() );

}

}

}

#### Flow

Si utilizza il componente per la creazione di record.

Per assegnare un valore booleano si dovrà specificare {! $GlobalConstant.True | .False }

#### API

Si utilizzano le stesse funzioni per l’inserimento di record, specificando come oggetto l’API del platform event. Per esempio: _endpoint_/services/data/_versione_/sobjects/EventMsg\_\_e

### Modalità di ricezione <a href="#toc162445421" id="toc162445421"></a>

Nella sezione _Subscriptions_ di un platform event sono elencati trigger, flow e processi in ascolto, ma non le API esterne. Solo per i trigger è possibile sospendere la sottoscrizione e riprenderla dall’ultimo messaggio processato oppure ignorandoli _(Resume from Tip)_

#### Apex Trigger

Si definiscono come per qualsiasi altro oggetto, ma possono essere solo del tipo after insert.

* In questo caso i trigger girano separatamente, di default sotto l’utente _Automated Process_, ed i log possono essere registrati solo assegnando una _trace flag_ ad esso; per le classi di test, invece, si ricade nel caso usuale.
* L’ordine dei record è stabilito dal _ReplayId_ ed essi vengono raccolti indipendentemente da cosa li abbia pubblicati, in batch di 2000 (modificabile) anziché di 200.
* La pubblicazione di messaggi di test va racchiusa tra i metodi Test.startTest() e .stopTest(), essa avverrà alla chiamata del secondo.

#### LWC ed Aura

Gli LWC hanno bisogno di importare i metodi subscribe, unsubscribe, onError, setDebugFlag, isEmpEnabled dal modulo lightning/empApi

Negli Aura Component si usa un \<lightning:empApi>

#### Flow

Un flow può essere specificatamente _Platform Event Triggered_, ma anche un flow di altro tipo può sottoscriversi ad un platform event attraverso un elemento _Pausa_, potendo specificare anche i criteri in base a cui riprendere il suo corso.

#### CometD e Pub/Sub API

Sono due tecnologie con cui app esterne, componenti Lightning e pagine Visualforce possono sottoscriversi ad un platform event.

* **CometD** utilizza un modello _push_, con cui il server aggiorna il client senza che questo debba interrogarlo in continuazione, facilitando la comunicazione **in tempo reale**
* **Pub/Sub API** utilizza un modello _pull_ con cui il client richiede attivamente al server di sincronizzarlo con un certo numero di eventi a seconda della propria capacità di calcolo, così da garantire la **stabilità del sistema** durante i **picchi di informazione**

### Note <a href="#toc162445422" id="toc162445422"></a>

*
  *
    *
      1. Si impostano da _Integrations > Platform Events_
      2. [Streaming API](https://developer.salesforce.com/docs/atlas.en-us.api\_streaming.meta/api\_streaming/intro\_stream.htm?\_ga=2.15550581.274857579.1704808033-724911337.1698240257), [Pub/Sub](https://developer.salesforce.com/docs/platform/pub-sub-api/overview?\_ga=2.15550581.274857579.1704808033-724911337.1698240257)
      3. CometD client & EMP Connector
      4. Classe EventBus
      5. Documentazione empApi per [LWC](https://developer.salesforce.com/docs/component-library/bundle/lightning-emp-api/documentation?\_ga=2.129870220.1222495550.1705232965-724911337.1698240257) ed [Aura](https://developer.salesforce.com/docs/component-library/bundle/lightning:empApi/documentation?\_ga=2.231049247.1222495550.1705232965-724911337.1698240257)
      6. Cfr. [Change Data Capture Event](https://trailhead.salesforce.com/content/learn/modules/change-data-capture)
