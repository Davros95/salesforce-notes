# Flow

| Tipo              | Lanciato da...                                                       | Descrizione                                                     |
| ----------------- | -------------------------------------------------------------------- | --------------------------------------------------------------- |
| Screen Flow       | - Quick action <br/> - Lightning page <br/> - Sito Experience Cloud… | Presentano una interfaccia grafica che guida gli utenti         |
| Autolaunched Flow | - Altro flow <br/> - Codice Apex <br/> - Rest API                    | Lavorano in background, non attivati direttamente da un trigger |
| Triggered Flow    | - Tempo e frequenza <br/> - Modifica record <br/> - Platform Event   | Lavorano in background una volta attivati da un trigger         |

| Opzione                     | Attiva il flow…                     | Da usare per…                                                                                                           |
| --------------------------- | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| Fast Field Update           | Prima del salvataggio               | Modifica sullo stesso record                                                                                            |
| Related Records and Actions | Dopo il salvataggio                 | - Modifiche su altri record <br/> - Chiamare subflows <br/> - Eseguire azioni programmate <br/> - Eseguire altre azioni |
| Run Asynchronously          | Dopo il salvataggio                 | Azioni particolari che rallenterebbero i processi se fossero sincrone                                                   |
| Scheduled Paths             | Dopo un certo tempo dal salvataggio | Eseguire azioni dopo un lasso di tempo, anche dipendente da criteri legati ai valori nei campi del record               |

### Altre opzioni:
- **When to Run the Flow for Updated Records**
  - Every time a record is updated and meets the condition requirements
  - Only when a record is updated to meet the condition requirements
- **How to Set the Record Fields**
  - *Use all values from a record* <br> Se i valori necessari sono tutti ricavabili da un record già esistente (anche quello che ha attivato il flow), oppure da una singola risorsa che andrà specificata e costruita
  - *Use separate resources, and literal values* <br> Se i valori necessari andranno ricavati da più record di vario tipo
- Un flow può essere fatto girare:
  - in *System Context with Sharing*, ignorando i permessi FLS e CRUD ma considerando le Sharing Rules 
  - in *System Context without Sharing*, ovvero vedendo tutto
  - in *User or System Context*, in base a dove è lanciato

### Note
- **Solo i record-triggered flow possono essere testati**
  - I test sono creati e rilanciati dalla pagina della versione desiderata, premendo *View Tests*
  - Si possono testare i path immediati e quelli programmati, ma non gli asincroni; non si può simulare la cancellazione di un record
- `$Record` e `$Record__Prior` sono variabili globali che contengono i valori correnti e antecedenti alla modifica di un record
Note varie
- LISTA OPZIONI DEBUG
- Il Default Workflow User è quello cui vengono assegnate le modifiche dovute alle azioni degli Scheduled Paths, se l’utente che li ha avviati non è più attivo. Si modifica in Process Automation Settings e non può essere disattivato.
- ⚠️ Uno Scheduled Path gira nel contesto di sistema, ignorando i permessi dell’utente che ha attivato il flow
- Nel Flow Trigger Explorer si possono verificare quali tra i percorsi Fast Field Update, Related Records and Actions, ed Asincroni sono interessati dalle operazioni di creazione/modifica dei record, oggetto per oggetto, e stabilire la sequenza di esecuzione per quelli concorrenti non Asincroni
- Una Interview non può essere salvata in tutti i casi, come per i flow attivati da un Platform Event
- La pagina Time-Based Workflow mostra quali azioni (non solo degli Scheduled Paths dei flow) sono programmate sui record dei singoli oggetti, permettendo anche di cancellarle.
- Un flow non può eseguire un callout se ha del lavoro in sospeso nella transazione corrente, per esempio una mail da inviare o un record da inviare al database. Nelle opzioni di una azione si può scegliere se Iniziare sempre una transazione nuova, Continuare sempre nella corrente, Lasciar decidere il flow (se si usano @InvocableMethod custom, devono avere l’attributo callout = true o non se ne “accorgerà”)
 
`code`