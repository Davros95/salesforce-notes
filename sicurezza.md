# Sicurezza <a href="#toc162445429" id="toc162445429"></a>

_Non confondere le sharing rules con FLS e CRUD!_

Considerando che il risultato finale è quello stabilito nell’ultima circostanza in cui il codice viene lanciato,\
le sharing rules **vengono considerate**:

Vengono **ignorate**:

* dagli _Scheduled Paths_ dei flow, anche se l’utente che figura è quello che li ha attivati

### Applicazione delle visibilità <a href="#ole_link46" id="ole_link46"></a>

| **Situazione**                                         | **FLS + CRUD**                                                           | **Sharing Rules**         |    |
| ------------------------------------------------------ | ------------------------------------------------------------------------ | ------------------------- | -- |
| **APEX**                                               | Execute Anonymous e Chatter                                              |                           | SÌ |
| Non dichiarato **con almeno un metodo @AuraEnabled**   | N/D                                                                      | = with sharing            |    |
| Non dichiarato **non ad inizio transazione**           |                                                                          | N/D (= inherited sharing) |    |
| Non dichiarato **ad inizio transazione**               |                                                                          | NO                        |    |
| Inherited sharing **ad inizio transazione**            | N/D                                                                      | SÌ                        |    |
| with sharing                                           | N/D                                                                      | SÌ                        |    |
| without sharing                                        | (NO)                                                                     | NO                        |    |
| Codice direttamente in trigger                         | NO                                                                       | NO                        |    |
| **VF**                                                 | Standard controller + estensioni                                         | NO                        | SÌ |
| Custom controller                                      | NO                                                                       | NO                        |    |
| **LWC**                                                |                                                                          |                           |    |
| Lightning Data Service                                 | SÌ                                                                       | SÌ                        |    |
| **DATABASE**                                           | <p>SOQL, SOSLWITH USER_MODE,WITH SECURITY_ENFORCED</p><p>DML as user</p> | SÌ                        | SÌ |
| <p>SOQL, SOSL WITH SYSTEM_MODE</p><p>DML as system</p> | NO                                                                       | N/D                       |    |
| SOQL DEFAULT                                           |                                                                          |                           |    |
| SOQL WITH SECURITY\_ENFORCED                           | Sì                                                                       | N/D                       |    |
| SOQL WITH USER\_MODE                                   | Sì                                                                       | Sì                        |    |
| DML DEFAULT                                            |                                                                          |                           |    |
| DML as user                                            | Sì                                                                       | Sì                        |    |
| DML as system                                          |                                                                          |                           |    |
| SOSL DEFAULT                                           |                                                                          |                           |    |
| **FLOW**                                               | Systemcontext with sharing                                               | NO                        | SÌ |
| System context without sharing                         | NO                                                                       | NO                        |    |
| User or System context                                 | N/D                                                                      | N/D                       |    |

### Calcolo e verifica dei permessi <a href="#toc162445431" id="toc162445431"></a>

Per verificare se l’utente corrente ha i permessi CRUD / FLS si possono chiamare specifici metodi sulle classi Schema .DescribeSObjectResult | .DescribeFieldResult (v. sezione apposita).

Per “ripulire” una lista di record da tutti quei campi che non sarebbero accessibili all’utente corrente si possono usare la classe SObjectAccessDecision ed il metodo Security.stripInaccessible:

SObjectAccessDecision ripuliti = Security.stripInaccessible(LIVELLO,records);

(dove LIVELLO = CREATABLE, READABLE, UPDATABLE, UPSERTABLE)

SObject\[] listaPronta = ripuliti.getRecords();

e verificare se specifici campi sono stati sbiancati valutando sui (singoli) record il metodo .isSet(campo)

### Mitigazione rischi di SOQL/SOSL Injection <a href="#toc162445432" id="toc162445432"></a>

Il problema principale risiede nella possibilità di aggiungere ad una query caratteri speciali e parole chiave che portino a chiedere informazioni non inizialmente previste.

* Query _statiche_ con _bind variables_\
  Non concatenare la variabile stringa alla query, ovvero ‘query iniziale’ + variabile,\
  ma chiamarla direttamente in fase di lancio come risultato = \[queryIniziale :var]
* varStringa.escapeSingleQuotes()\
  Questo metodo trasforma ogni ‘ in \’ costringendo il contenuto ad essere considerato come una stringa nella query
* _Type casting_\
  Dove possibile, convertire forzatamente l’input nel tipo desiderato e atteso, poi di nuovo in stringa prima di inserirlo nella query
* Rimuovere caratteri potenzialmente indesiderati _(Blocklisting)_\
  Per esempio, se ci si aspetta una sola parola, rimuovere tutti gli spazi
* _Allowlisting_\
  Se possibile, eseguire controlli che accettino solo particolari input e respingano gli altri
