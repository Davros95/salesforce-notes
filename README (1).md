---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Org, sandbox e sviluppo

I **tipi di org** sono _Production, Trial, Sandbox, Scratch, Developer org_ (non confondere con il tipo di sandbox!) e _Partner Developer org_ (più ricca).

I **tipi di sandbox** associati ad una org di produzione sono, con alcune caratteristiche:

<table data-header-hidden><thead><tr><th></th><th></th><th width="188"></th><th></th><th></th></tr></thead><tbody><tr><td></td><td><strong>Uso</strong></td><td><strong>Spazio</strong></td><td><strong>Dati iniziali</strong></td><td><strong>Int. refresh</strong></td></tr><tr><td><strong>Developer</strong></td><td>Sviluppo</td><td>200 MB tra file + dati</td><td>-</td><td>1 giorno</td></tr><tr><td><strong>Developer</strong><br><strong>Pro</strong></td><td><p>Sviluppo e integrazione</p><p>Piccoli QA</p></td><td>1 GB tra file + dati</td><td>-</td><td>1 giorno</td></tr><tr><td><strong>Partial</strong><br><strong>Copy</strong></td><td><em>Quality Assurance</em>:<br>test e training</td><td><p>5 GB di dati</p><p>File quanto PROD</p></td><td>A scelta da template<br>(fino a 10k record/ogg.)</td><td>5 giorni</td></tr><tr><td><strong>Full</strong><br><strong>Copy</strong></td><td><p><em>Staging</em>: test finale</p><p>Performance vs. carico</p></td><td>Quanto PROD</td><td>Tutti o a scelta da template</td><td>29 giorni</td></tr></tbody></table>

* Il _Sandbox Template_ permette di indicare solo specifici oggetti di cui ricopiare metadati e record; è utilizzabile solo nelle _Partial Copy_ (obbligatorio) e nelle _Full_ (facoltativo)
* Il cestino ospita un numero di record calcolato come _data storage in MB x 25_, es. 2 GB 🡪 50k record
* Quasi tutti i record pesano 2 KB ciascuno

### Rilasci <a href="#toc162445381" id="toc162445381"></a>

#### Modelli di sviluppo

* **Change Set Development Model**
* **Org DM:** il codice sorgente risiede solo in sandbox ed org, si ottiene e rilascia con la CLI ma non è tracciato in Git
* **Package DM**: la “source of truth” è Git, e si sviluppa, testa e rilascia tra sandbox, scratch org e produzione passando da esso e servendosi della CLI

#### Change Sets

* La **connessione** tra org va autorizzata sempre dal lato di quella in arrivo: da _Deployment Settings_ si seleziona la org di partenza e si abilita _Allow Inbound Changes_ (dall’altro lato si attiverà la casella di sola lettura _Accept Outbound Changes_). Per abilitare nel verso opposto, si devono fare queste operazioni nell’altra org.
* ⚠️ Non possono essere usati per cancellare o rinominare metadati
* Si definiscono i componenti e, separatamente, i profili di un change set da _Outbound Change Sets_ e lo si invia ad una org con _Upload_. Deve però essere validato e deployato in quest’ultima, da _Inbound Change Sets_

#### Managed ed Unmanaged Packages

| **Managed**                                                                                            | **Unmanaged**                                  |
| ------------------------------------------------------------------------------------------------------ | ---------------------------------------------- |
| I componenti sono protetti                                                                             | Sono completamente visibili e modificabili     |
| Si possono offrire aggiornamenti e patch                                                               | Non esistono aggiornamenti                     |
| I componenti hanno un proprio namespace, definito dallo sviluppatore, es: namespace\_\_ObjectName\_\_c | Non hanno un namespace proprio                 |
| Se ne può sviluppare uno per Developer Edition                                                         | Se ne possono sviluppare a piacere (in una DE) |

* Una sandbox fornisce lo stesso numero di licenze previste in produzione quando è generata; se se ne sono acquistate altre lo si può aggiornare da _Company Information > Match production licenses_
* Il _refresh_ propriamente detto è da produzione, e permette di scegliere un nuovo tipo di sandbox; se in ingresso si sceglie una sandbox, si farà un _clone_ e l’unico tipo disponibile sarà lo stesso della sandbox
