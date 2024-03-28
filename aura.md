# Aura components <a href="#toc162445388" id="toc162445388"></a>

## File <a href="#toc162445389" id="toc162445389"></a>

I principali sono:

* _Component_ _/ App_ (Nome .cmp|.app)
* markup del componente oppure di una app stand-alone
* esplicita legami con eventuali CSS e JS e le opzioni di configurazione
* _Controller_ (ComponentController.js)
* per operare con eventi JS lato client
* mappa _nomeFunzione_ : function(component, event, helper){_codice_…}
* _Helper_ (ComponentHelper.js)
* funzioni accessibili da qualsiasi altro punto del JS
* mappa come sopra
* _Style_ (Component.css): proprietà grafiche del markup del componente

Gli altri sono:

* Component.auradoc
* una sorta di guida al componente e al codice di cui è costituito
* le punta la URL \<myDomain>.lightning.force.com/componentReference/suite.app
* Component.design:
* opzioni di configurazione
* attributi esposti al Lightning App Builder
* per indicare restrizioni ai dispositivi supportati ed all’uso nella sola record page
* Component.svg: icona personalizzata in SVG
* ComponentRenderer.js: codice per modificare il rendering di default

## Elementi notevoli del codice <a href="#toc162445390" id="toc162445390"></a>

### Markup

* {}
* \<aura:application>\
  Racchiude tutto il markup per _un’applicazione_ (accessibile da _nomeIstanza_/c/_nomeApp_.app_)_
* access = public | global
* controller = _namespace_._nomeController_ (per Apex)
* extends = _namespace_:_nomeApp_; ltng:outApp per includerla in Visualforce
* extensible (Boolean), default false
* implements = int1, int2, … : vedi le _interfacce_
* \<aura:component>\
  Markup per un _componente_
* \<c:_componenteFiglio_>\
  Inserisce un componente figlio definito separatamente
* \<aura:attribute>\
  ⚠️Deve corrispondergli un \<design:attribute> nel _design_
* \<aura:event>
* access, extends (come sopra)
* type = COMPONENT | APPLICATION
* \<aura:interface>
* access, extends
* \<aura:dependency>\
  Da usare quando ci si appoggia ad un elemento che non è menzionato esplicitamente nel markup e si vuole istanziare il componente “indirettamente”, per esempio tramite JS in una Visualforce
* resource = indirizzo
* type = COMPONENT (def) | APPLICATION | EVENT | INTERFACE | MODULE (LWC)
* \<ltng:require>
* scripts=”{!$Resource.script1}, {!$Resource.script2, …}”\
  per JS esterno, da caricare come Static resource
* ![A screenshot of a computer program

  Description automatically generated](.gitbook/assets/0.png)

Interfacce

* **Visibilità**
* flexipage:availableForAllPageTypes: in ogni Lightning Page + utility bar
* flexipage:availableForRecordHome: solo nelle Record Page
* clients:availableForMailAppAppPage: per mail app, anche esterne
* **Accesso al record**
* force:hasRecordId: per accedere all’Id
* force:hasSObjectName: per accedere all’Api Name

### Design

* \<design:attribute>\
  name viene referenziato nel codice del componente, label figura nelle proprietà, nell’App Builder
* \<design:supportedFormFactors> con \<design:supportedFormFactor>\
  Large e/o Small rispettivamente per supportare solo fisso o mobile; se non presenti, ereditano dalla pagina
* \<sfdc:objects> con \<sfdc:object>\
  Per specificare gli Api Name di specifici oggetti a cui restringere il supporto, per Record page

## Eventi

* Quelli dei _Lightning Components_ possono essere recepiti e gestiti soltanto all’interno dello stesso componente oppure dalla gerarchia dei suoi contenitori
* Quelli dell’applicazione possono essere gestiti da qualunque componente che abbia una opportuna funzione nel proprio handler
* init\
  Dal componente più interno fino all’applicazione
* render
* rerender
* afterRender
* unrender
* [components\_devconsole\_configs.htm](https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lightning/components\_devconsole\_configs.htm)