# [SOSL](https://developer.salesforce.com/docs/atlas.en-us.soql\_sosl.meta/soql\_sosl/sforce\_api\_calls\_sosl.htm?\_ga=2.247912135.1390319515.1693192700-850010389.1693188698) <a href="#toc162445437" id="toc162445437"></a>

Sta per _Salesforce Object Search Language_. Fa riferimento all'_indice di ricerca_, in cui i valori di specifici campi di oggetti sono stati _indicizzati_, ovvero analizzati e trattati in modo da potervi accedere più velocemente ed ammettendo una ricerca per approssimazione, includendo variazioni dei termini.\
Le ricerche si avvalgono di _gruppi di sinonimi_ già forniti da SF ma che possono essere ampliati dalla pagina **Synonyms** nel Setup.

Per ricerche:

1. quando non si conoscono i campi dove si trovano i dati
2. su più oggetti anche non correlati fra loro

Una ricerca SOSL è _case insensitive_ e rispetta i permessi dell’utente e le sharing rules

definizione

FIND …

\[ IN … FIELDS ]

\[ RETURNING

Ogg1 (campi… \[ WHERE… ORDER BY… USING ListView=… LIMIT… ]),

Ogg2 (campi… \[ WHERE… ORDER BY… USING ListView=… LIMIT… ])…

]

\[LIMIT n ]

\[ _se un solo oggetto:_ OFFSET n ]

1. FIND …\
   Testo da cercare, tra {…} per la API e tra ‘…’ singoli in Apex
2. Caratteri speciali: \* corrisponde a 0+ caratteri, ? ad uno
3. “ ”: ricerca esatta di frasi
4. Operatori logici: AND, OR, AND NOT, ( ) per logica complessa
5. ⚠️ Non più di 4000 caratteri per far funzionare gli operatori logici, massimo 10000
6. ⚠️ Escape con \ , degli operatori logici con “ ”
7. IN ALL | NAME | EMAIL | PHONE | SIDEBARFIELDS\
   Restringe i tipi di campo in cui cercare, il default è ALL_._
8. I campi NAME comprendono alcuni specifici per oggetti standard e quelli definiti _Name fields_ nei custom
9. ⚠️I campi numerici non vengono considerati
10. RETURNINGOgg1 (campi ed istruzioni), Ogg2 (…), …\
    Per restringere la ricerca su specifici oggetti e loro campi, di default avviene quasi ovunque.\
    Per ogni oggetto si possono specificare tra parentesi ( ):
11. i campi da restituire; se almeno uno è presente:
12. WHERE campi ed operatori di confronto
13. ORDER BY campo \[ASC | DESC] \[NULLS first | last]
14. OFFSET n\
    Salta i primi _n_ record tra i risultati; utile in combinazione con LIMIT.\
    Si può usare se si cerca in un solo oggetto, e deve stare in fondo
15. USING ListView= nome ListView | Recent: filtra secondo una ListView dell’utente
16. LIMIT n: se non presente, il limite di record per l’oggetto è una quota parte del limite complessivo (vedi sotto)
17. ⚠️ Gli oggetti che devono essere esplicitamente riportati in un RETURNING sono: _Articles, Documents, Feed Comments, Feed Items, Files, Products, Solutions_ ed oggetti esterni
18. LIMIT n: limite complessivo dei record da restituire, al più 2000 (che è il valore di default)

**NOTA:** Nelle classi Apex, la query può essere racchiusa tra \[…] oppure passata come stringa al metodo Search.find()

### Suggested results API <a href="#toc162445438" id="toc162445438"></a>

* **Search Suggested Records**\
  Ricerca generale nei record
* **Search Suggested Article Title Matches** (/vXX.X/search/suggestTitleMatches)\
  Nella Knowledge Base
* **SObject Suggested Articles for Case**\
  Nella Knowledge Base, specificatamente per i Case

### Note <a href="#toc162445439" id="toc162445439"></a>

1. In caso di ricerca su più oggetti, in Apex il risultato sarà restituito come List\<List\<sObject>>, ma si potrà eseguire facilmente il casting negli oggetti corretti considerando che le liste saranno ordinate nella stessa sequenza di RETURNING Ogg1 (…), Ogg2 (…), …
2. Si possono indicare degli articoli nella KB come _promoted_, ovvero in cima ai risultati di ricerche con termini specifici
