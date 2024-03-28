# Custom Settings / metadata Types <a href="#toc162445399" id="toc162445399"></a>

Caratteristiche principali:

* Custom Settings:
* Utile per configurazioni globali o specifiche per profilo/utente all'interno di un'organizzazione.
* Facile da configurare e gestire attraverso l'interfaccia utente di Salesforce.
* Adatto per configurazioni che possono essere modificate più frequentemente e che richiedono un accesso di lettura/scrittura diretto.
* Custom Metadata Types:
* Ideale per la gestione di configurazioni di livello enterprise o per il versioning di configurazioni tra organizzazioni.
* Requisisce una migrazione di metadati per l'aggiornamento, offrendo una maggiore stabilità dei dati di configurazione.
* Adatto per configurazioni complesse e persistenti che richiedono una struttura dati più avanzata.

### Note sui CMT <a href="#toc162445400" id="toc162445400"></a>

* Se ne possono fare query normalmente ma anche chiamarvi specifici metodi:
* .getAll: mappa tutti DeveloperName ⬄ sObject
* .getInstance(recordId | DeveloperName | qualifiedApiName)

### Note sui CS <a href="#toc162445401" id="toc162445401"></a>

1. While custom settings data is included in sandbox copies, it is treated as data for the purposes of Apex test isolation. Apex tests must use SeeAllData=true to see existing custom settings data in the organization. As a best practice, create the required custom settings data in your test setup.
