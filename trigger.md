
# Trigger

trigger TriggerName on ObjectName (eventi) {}

### Eventi: <a href="#toc162445444" id="toc162445444"></a>

| before: modifiche prima di salvare nel database (sui record di lavoro verranno considerate in automatico)        | <p>insert</p><p>update</p><p>delete</p> | - |
| ---------------------------------------------------------------------------------------------------------------- | --------------------------------------- | - |
| after: accesso a campi di sistema o per modificare di conseguenza altri record (quelli iniziali sono in lettura) | after undelete                          |   |

#### Variabili di contesto sull’oggetto Trigger:

| .new, .newMap                               | lista record / mappa id -> nuovi dopo insert, update o recuperati dopo undelete   |
| ------------------------------------------- | --------------------------------------------------------------------------------- |
| .old, .oldMap                               | lista record / mappa id -> record cancellati, vecchie versioni prima di un update |
| .isBefore/.isAfter, .isInsert/.isUpdate/... | valore booleano che dipende dal tipo                                              |
| .operationType                              | in alternativa a sopra, restituisce BEFORE\_INSERT, AFTER\_DELETE ecc.            |
| .isExecuting                                | booleano che dà **vero** se il trigger su cui è chiamato è in funzione            |
| .size                                       | somma di record vecchi e nuovi lavorati dal trigger                               |

#### Metodi correlati

| oggetto.addError(testo \| fieldname, testo) | Mostra un errore (in cima alla finestra o accanto ad un campo specifico) ed esegue il rollback puntuale sui dati (se disattivo successo parziale) |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |

#### Note:

1. È importante creare un solo trigger per oggetto e definire le singole casistiche al suo interno, richiamandosi a classi “handler” esterne
2. Scrivere il codice supponendo che i record lavorati siano sempre una lista, e non singoli
3. Non inserire operazioni DML di insert/update sui record nel codice del trigger da essi stessi chiamato (se invece dei dati vengono impiegati su un altro oggetto, per esso è possibile)
4. I trigger lavorano con blocchi di massimo 200 record alla volta; per numeri maggiori vengono eseguiti più volte
5. Per forzare l’accensione o spegnimento di un trigger nel codice chiamare su di esso il metodo .compute = true | false (lett. ON/OFF)
6. After insert 🡪 read-only su quelli di partenza

#### Dubbi:

1. Fin dove si spingono “Trigger.new/.old” ? In SOQL il sistema accede agli Id se scritto “WHERE Id In :Trigger.New” ma sono liste di oggetti; se provo a riferirmi ad un altro campo?

# Custom settings / metadata <a href="#toc162445445" id="toc162445445"></a>

![Immagine che contiene testo, schermata, Carattere, informazione

Descrizione generata automaticamente](.gitbook/assets/5.png)

* [https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_customsettings.htm](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex\_customsettings.htm)
* [https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_methods\_system\_custom\_settings.htm](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex\_methods\_system\_custom\_settings.htm)
