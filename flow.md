# Flow

| Tipo              | Lanciato da...                                                       | Descrizione                                                     |
| ----------------- | -------------------------------------------------------------------- | --------------------------------------------------------------- |
| Screen Flow       | - Quick action <br> - Lightning page <br> - Sito Experience Cloud… | Presentano una interfaccia grafica che guida gli utenti         |
| Autolaunched Flow | - Altro flow <br> - Codice Apex <br> - Rest API                    | Lavorano in background, non attivati direttamente da un trigger |
| Triggered Flow    | - Tempo e frequenza <br> - Modifica record <br> - Platform Event   | Lavorano in background una volta attivati da un trigger         |

| Opzione                     | Attiva il flow…                     | Da usare per…                                                                                                           |
| --------------------------- | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| Fast Field Update           | **Prima** del salvataggio           | Modifica sullo stesso record                                                                                            |
| Related Records and Actions | **Dopo** il salvataggio             | - Modifica sullo stesso record **⚠️Diversamente dai flow** <br> - Modifiche su altri record <br> - Chiamare subflows <br> - Eseguire azioni programmate <br> - Eseguire altre azioni |
| Run Asynchronously          | Dopo il salvataggio                 | Azioni particolari che rallenterebbero i processi se fossero sincrone                                                   |
| Scheduled Paths             | Dopo un certo tempo dal salvataggio | Eseguire azioni dopo un lasso di tempo, anche dipendente da criteri legati ai valori nei campi del record               |

## Altre opzioni:
- **When to Run the Flow for Updated Records**
  - Every time a record is updated and meets the condition requirements
  - Only when a record is updated to meet the condition requirements
- **How to Set the Record Fields**
  - *Use all values from a record*
  <br> Se i valori necessari sono tutti ricavabili da un record già esistente (anche quello che ha attivato il flow), oppure da una singola risorsa che andrà specificata e costruita
  - *Use separate resources, and literal values*
  <br> Se i valori necessari andranno ricavati da più record di vario tipo
- Un flow può essere fatto girare:
  - in *System Context with Sharing*, ignorando i permessi FLS e CRUD ma considerando le Sharing Rules 
  - in *System Context without Sharing*, ovvero vedendo tutto
  - in *User or System Context*, in base a dove è lanciato



## Componenti Notevoli

### Resources
Tutti i "contenitori" che devono essere valorizzati durante l'esecuzione del flow. Alcune possono essere rese *Available for input | output* da o verso l'esterno.
<br> Le espressioni sono rese con `{! ... }`

- **Variabili**
  - **Boolean**
  <br> ⚠️ Usare `$GlobalConstant.True | False`
- **Costanti**
- **Formule**
- **Text Templates**
  <br> Costanti di testo formattato da usare ... e nei post di Chatter (come testo normale). Permettono di usare *merge fields*

Sono accessibili a parte le **variabili globali** come:
- **`$User`, `$UserRole`**
- **`$Profile`, `$Permission` (custom)**
- **`$Organization`:** informazioni sulla org
- **`$Flow`:** informazioni sulla *Flow interview* corrente
- **`$Label`**
- **`$Input`, `$Output`:** fa accedere ai valori in ingresso e uscita dal flow
- **`$Record`, `$Record__Prior`:** valore corrente del record in uso, e antecedente alla modifica

Infine:
- Esistono le **Costanti globali** **`$GlobalConstant.True | False | EmptyString`**
- I **Custom Metadata** sono accessibili per mezzo di *Get Records*

> **Sistemare**
> <br> ✳️ `recordId`, **input**, popolata in automatico quando si lancia un flow da un action button

### Actions
- **Email Alerts**
  <br> Usano gli stessi template dei workflow
- **Post to Chatter**
  - **Message:** usa la risorsa *Text Template* che deve essere impostata da visualizzare come *plain text* (le menzioni si creano con `@[...]`)
  - **Target Name or ID:** ID record, utente (anche username), gruppo (anche nome) nel cui feed verrà aggiunto il post
  - **Target Type:** Specificare `User` o `Group` (senza `'$'`) solo se sono stati scelti come *Target Name or ID*
- **Submit for Approval**
  - **Record ID**
  - **Approval Process Name or ID:** l'AP da usare, altrimenti sarà il primo per cui saranno soddisfatte le condizioni di ingresso
  - **Submitter ID:** se specificato, sostituisce l'utente che fa girare il flow

### Assignment Element
- *Add*, *Subtract* funzionano tra valori numerici, currency e data-numero (di giorni)
- L'opzione *Add* permette anche di concatenare due stringhe; se un valore è di altro tipo si deve passare dalla formula `TEXT(...)`

### Subflow Element
I *Subflow* sono flow secondari (figli) in cui viene accentrata una porzione di logica comune a diversi flow.
- Le variabili del flow figlio impostate come *available for input* possono essere attivate e valorizzate nell'elemento *Subflow*, nel padre
- Solo i flow **Autolaunched e Screen** possono essere impiegati come subflow, in particolare i flow **Screen** possono richiamare solo altri **Screen**
- Un subflow inattivo può essere avviato solo dagli utenti con il permesso *Manage Flows*
- Se un subflow non ha alcuna versione attiva, viene considerata **la più recente**.


## Operazioni con i record

### Get Records
- **How to store record data**
  - *All fields*: la norma, a meno di oggetti con **centinaia** di campi custom → record in uscita
  - *Choose fields*: seleziona solo certi campi → record in uscita
  - *Choose fields and assign variables*: seleziona campi → li assegna a variabili separate

### Update Records
- **How to Find Records to Update and Set Their Values**
  - *Update records related to the (..tipoRecord..) record that triggered the flow*
  <br> Sia per i **record genitori** (specificando le lookup/MD) che **figli** (eventualmente filtrare con delle condizioni)
  - *Use the IDs and all field values from a record or record collection*
  <br> Se sono già stati dichiarati e popolati precedentemente nel flow
  - *Specify conditions to identify records, and set fields individually*
  <br> Include una chiamata al database come per *Get Records*
⚠️ Non si comporta come *upsert*, gli Id devono essere presenti!


> Se un elemento prevede di restituire una variabile in uscita (per es. di tipo *record*) questa è resa automaticamente disponibile nelle *resources* e visualizzata come:
> <br> - *Nome variabile* from *API Name elemento*
> <br> - `{! APINameElemento }`

## Note
- **Test, solo per i *Record-Triggered Flow***
  - I test sono creati e rilanciati dalla pagina della versione desiderata, premendo *View Tests*
  - Si possono testare i path immediati e quelli programmati, ma non gli asincroni
  - Non si può simulare la cancellazione di un record

- Il Default Workflow User è quello cui vengono assegnate le modifiche dovute alle azioni degli Scheduled Paths, se l’utente che li ha avviati non è più attivo. Si modifica in Process Automation Settings e non può essere disattivato.
- ⚠️ Uno Scheduled Path gira nel contesto di sistema, ignorando i permessi dell’utente che ha attivato il flow
- Nel Flow Trigger Explorer si possono verificare quali tra i percorsi Fast Field Update, Related Records and Actions, ed Asincroni sono interessati dalle operazioni di creazione/modifica dei record, oggetto per oggetto, e stabilire la sequenza di esecuzione per quelli concorrenti non Asincroni
- Una Interview non può essere salvata in tutti i casi, come per i flow attivati da un Platform Event
- La pagina Time-Based Workflow mostra quali azioni (non solo degli Scheduled Paths dei flow) sono programmate sui record dei singoli oggetti, permettendo anche di cancellarle.
- Un flow non può eseguire un callout se ha del lavoro in sospeso nella transazione corrente, per esempio una mail da inviare o un record da inviare al database. Nelle opzioni di una azione si può scegliere se Iniziare sempre una transazione nuova, Continuare sempre nella corrente, Lasciar decidere il flow (se si usano @InvocableMethod custom, devono avere l’attributo callout = true o non se ne “accorgerà”)