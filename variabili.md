
# Variabili <a href="#toc162445446" id="toc162445446"></a>

### Tipi primitivi <a href="#toc162445447" id="toc162445447"></a>

* _Blob_
* _Boolean_
* _Date_, _Time_, _Datetime_
* Interi: _Integer_ (32 bit), _Long_ (64); decimali: _Decimal_, _Double_ (64 bit),
* _ID_
* _String_
* _Object_

#### Note

1. Il valore di un campo _Currency_ è automaticamente assegnato al tipo _Decimal_

### Collezioni

#### Liste

Sinonimi di array. Si possono inizializzare in questi modi:

| <p>List&#x3C;String> myStrings<br></p><p>String[] myStrings</p> | = | new List\<String>(); //vuota |
| --------------------------------------------------------------- | - | ---------------------------- |
| new List\<String>{'S1','S2', …}; //con valori                   |   |                              |
| new String\[] {'S1','S2', …};                                   |   |                              |
| \[… QUERY …]                                                    |   |                              |

**⚠️ Ricorda: o le tonde, o le graffe**

Metodi

1. .add(elemento), .addAll(listaElementi)

#### Set

Non sono ordinati (e non si può accedere agli elementi con un indice); gli elementi devono essere unici.

Set\<ID> accountIds = new Set\<ID>{'001d000000BOaHSAA1**','**001d000000BOaHTAA1'};

#### Mappe

La _chiave_, primitiva, si collega al _valore_, di qualsiasi tipo.

Si possono popolare automaticamente scrivendo in questo modo:

Map\<Id, Account> accountMap = new Map\<Id, Account>(\[SELECT Id, Name FROM Account]);

Metodi

| m.put(1, 'First entry')       |   |
| ----------------------------- | - |
| m.containsKey(1)              |   |
| m.get(2)                      |   |
| Set\<Integer> s = m.keySet(); |   |

### Enums

Sono un tipo astratto e consistono in una lista determinata ed immutabile di _identificatori_ (valori, classi…); una volta definito uno, gli viene automaticamente associato un nuovo tipo di dato.

_\<accessModifier>_ enum nomeEnum = { e1, e2, … } ⬄ nomeEnum n = nomeEnum.e1;

### sObjects <a href="#toc162445450" id="toc162445450"></a>

* Rappresentano la classe genitore dei singoli oggetti
* Se generici si può accedere ai loro campi solo con put() e get(), se se ne esegue il cast in un tipo specifico con (tipoSpecifico)genericoSObject si può usare la notazione a punto

### Campi composti

Sono quelli di tipo _Address_ e _Geolocation_ e vengono automaticamente popolati secondo i valori dei componenti, per esempio BillingCountry ⬄ proprietà “country” . Nel loro insieme sono in sola lettura, perciò se vanno modificati si deve agire sui singoli componenti.

* I componenti di un campo _Geolocation_ di nome nomeCampo\_\_c hanno API Name come\
  nomeCampo\_\_Latitude\_\_s | Longitude\_\_s (senza \_\_c!) 🡪 proprietà “latitude” | “longitude”
* I campi _Address_ di tipo custom devono essere abilitati nella org

### Note <a href="#toc162445452" id="toc162445452"></a>

1. Un oggetto si può valorizzare in fase di creazione così:\
   Account acct = new Account(Field1 = ‘val1’, Field2 = ‘val2’, …);
2. Un ciclo for…each su una lista si rende con for (elType element : list) {}
3. Al contrario del JS dove avrebbero uno _scope_ diverso, tentare di ridefinire una variabile già definita in un blocco più esterno (es. fuori e dentro un ciclo for) dà errore di compilazione
