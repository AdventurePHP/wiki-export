Das APFelSMS ist eine im Sommer 2012 von [Jan
Wiese](/Benutzer:Jw-lighting "wikilink") entwickelte Erweiterung, die
dem Anwender alle mit der Seitenstruktur einer Website in Verbindung
stehenden Arbeiten abnehmen soll. Dazu gehört unter anderem das Erzeugen
von Navigationen (auch so genannter
[Brotkrümmel-Navigationen](http://de.wikipedia.org/wiki/Brotkr%C3%BCmelnavigation#Location_.28Ort.29))
sowie des

<title>

-Tags, die Einbindung von CSS- oder JavaScript-Dateien oder die
Einbindung von einzelnen Links zu anderen Seiten im Seitenbaum der
Homepage, um nur einige Features zu nennen. Die Verwendung des APFelSMS
erfolgt fast ausschließlich über Taglibs, eine umfangreiche API der
APFelSMS-Module erlaubt aber auch die Einbindung in Documentcontrollern,
Frontcontroller-Filtern oder selbst implementierten Taglibs. Durch die
konsequente Verwendung des DIServiceManagers und Programmierung gegen
Interfaces lassen sich einzelne Module vom Anwender leicht durch eigene
Implementierungen austauschen. Das APFelSMS ist so unkompliziert
erweiterbar und an die jeweiligen Anforderungen anpassbar. Der Name soll
soviel bedeuten wie "**A**dventure **P**HP **F**ramework
**S**ite-**M**anagement-**S**ystem" und wurde zudem inspiriert durch den
Nicknamen des APF-Entwicklers Florian Horn, *APFelsahne*.

# Voraussetzungen

### Versions-Serie v0.\*

-   APF-Version 1.16 (oder ab Rev. 1808), betrifft im wesentlichen die
    Service-Komponenten des APF und die Signatur der Klasse Taglib
-   tools::link LinkGenerator
-   tools::request RequestHandler
-   tools::string StringAssistant

<div style="background: #ff8; border: 2px solid #fb3; color: #333; margin: 1em 0 2em 0; min-height: 2em; padding: 8px">

Diese Dokumentation beschreibt das APFelSMS für die Verwendung mit
APF-Versionen &gt;= 2.0. Für APF-Versionen &lt; 2.0 unterscheiden sich
vor allem die Angabe von Namespaces. Die alte Dokumentation findet sich
hier: [APFelSMS Versionen
0.\*](http://wiki.adventure-php-framework.org/de/APFelSMS_v0)

</div>

### Versions-Serie v1.\*

-   APF-Version 2.0
-   APF\\tools\\link\\
-   APF\\tools\\request\\RequestHandler
-   APF\\tools\\string\\StringAssistant

Das APFelSMS lässt sich grundsätzlich auch ohne Frontcontroller
verwenden! Um die volle Funktionalität aus zu nutzen, ist die Verwendung
des Frontcontrollers aber empfehlenswert.

# Komponenten

Das APFelSMS besteht aus den folgenden Komponenten, die durch
verschiedene Klassen repräsentiert werden. Diese werden im Anschluss
kurz vorgestellt.

-   Seiten (Page)
-   Seitendekoratoren (PageDec)
-   Webseite (Site)
-   Link-Schemen
    ([LinkSchemes](http://adventure-php-framework.org/Seite/138-Links#Chapter-2-3-LinkScheme),
    siehe auch
    [Links](http://adventure-php-framework.org/Seite/138-Links) in der
    Komponenten-Dokumentation)
-   Seitenzwischenspeicher (PageStore)
-   Verwaltungskomponente (Manager)
-   Schnittstelle zur Datenschicht (Mapper)

### Seiten (Page)

Was eine Seite (**Page**) ist, braucht nicht erklärt zu werden ;) Die
Objektrepräsentation einer Seite im APFelSMS enthält neben ihren
Eigenschaften ID, Titel, Navigationstitel, CSS- und JS-Dateien
zahlreiche zusätzliche API-Methoden, die von Dekoratoren genutzt werden.
Es lassen sich weitere Seitentypen erstellen und gemeinsam mit anderen
nutzen, die weitere Daten ihrer Anwendung abbilden können. So ist zum
Beispiel die Verbindung der Seiten zu Produkten in einem Webshop
denkbar.

### Dekoratoren (PageDec)

Das Grundkonzept des APFelSMS basiert auf dem
[Decorator-Pattern](http://de.wikipedia.org/wiki/Decorator). So können
einer Seite zusätzliche Eigenschaften und Funktionen hinzugefügt und
beliebig kombiniert werden. Seitendekoratoren (**PageDec**) werden gemäß
des Patterns um das eigentliche Seitenobjekt wie die Schalen einer
Zwiebel herum gelegt. Das APFelSMS unterscheidet nicht zwischen
Seitenbaum und Struktur der Navigationen. Daher lassen sich Seiten wie
404- bzw. 403-Fehlerseiten mittels eines Hidden-Dekorator in den
Navigationen verstecken. Andere Dekoratoren stellen Referenzen zu
anderen Seiten im Seitenbaum her, verlinken zu externen URLs,
realisieren einen Zugriffsschutz oder fügen der URL zusätzliche
Request-Parameter hinzu. Eigene Dekoratoren lassen sich leicht ergänzen.

### Webseite (Site)

Das die Webseite repräsentierende **Site**-Objekt hält Eigenschaften wie
Startseite, aktuelle Seite, 404- und 403-Fehlerseiten und natürlich den
Titel der Webseite vor. Diese Eigenschaften werden teils über den
DIServiceManager injiziert und in der zugehörigen Konfiguration
definiert. Zusammen mit der Verwaltungskomponente, dem Manager bietet
sie wesentliche API-Methoden, die bei der Entwicklung von Taglibs
hilfreich sind.

### Link-Schemen (LinkScheme)

Link-Schemen (**LinkScheme**) sind eine Teilkomponente des APF
Link-Generators und definieren das Aussehen einer URL. Das APFelSMS
nutzt zur Erzeugung von Links den Link-Generator und liefert eigene
Link-Schemen mit. Details dazu entnehmt bitte der
[Komponenten-Dokumentation](http://adventure-php-framework.org/Seite/138-Links).

### Seitenzwischenspeicher (PageStore)

Der Seitenzwischenspeicher (**PageStore**) dient als Cache für Seiten
(und ihre Dekoratoren) und verwaltet diese. Er stellt ebenfalls sicher,
dass es für jede Seiten-ID nur ein Page-Objekt gibt.

### Verwaltungskomponente (Manager)

Der **Manager** ist DIE Kernkomponente des APFelSMS. Er liest die
aktuelle Seite aus dem Request aus und kümmert sich um die Erzeugung der
Seiten und ihrer Dekoratoren. Dazu greift er auch auf den Mapper zurück.

### Schnittstelle zur Datenschicht (Mapper)

Der **Mapper** liest die Seitenstruktur und die Eigenschaften der Seiten
und ihre(r) Dekoratoren aus der Datenschicht aus und injiziert sie in
die Objekte. Ein Mapper für XML-Dateien liegt bei, andere für weitere
Datenschichten sind in Planung.

# Installation

Das APFelSMS wird ab Version 1.16. des APF im Namespace
*extensions\\apfelsms* ausgeliefert. Wie bereits erwähnt, verwendet das
APFelSMS den
[DIServiceManager](http://adventure-php-framework.org/Seite/107-Services#Chapter-4-3-DIServiceManager-Komplexe-Services).
Zur Installation ist es nur noch notwendig, die Servicekonfiguration
einzurichten. Diese umfasst vor allem die Services für Seiten und
Dekoratoren. Diese können einfach übernommen werden. Nur bei eigenen
Implementierungen müssen diese verändert oder ergänzt werden. Einige
Einstellungen des APFelSMS werden in den Servicekonfigurationen von
Manager, Site und Mapper vorgenommen, und vom DIServiceManager
injiziert.

## Schritt 1: Servicekonfiguration kopieren

Im ersten Schritt der Installation müssen die beiliegenden
Konfigurationsdateien kopiert werden. Sie finden sich im Namespace
*extensions\\apfelsms\\config\\biz*. Das APFelSMS erwartet die
Konfiguration unterhalb des Namespaces *config\\extensions\\apfelsms*
wie folgt:

-   '''
    *config\\extensions\\apfelsms\\{CONTEXT}\\{ENVIRONMENT}_serviceobjects.ini*:'''
    Konfiguration für Manager, PageStore, Mapper und Site. Hier finden
    sich auch verschiedene Einstellungen.
-   '''
    *config\\extensions\\apfelsms\\pages\\{CONTEXT}\\{ENVIRONMENT}_serviceobjects.ini*:'''
    Konfiguration für den Service der Page-Komponente
-   '''
    *config\\extensions\\apfelsms\\pages\\decorators\\{CONTEXT}\\{ENVIRONMENT}_serviceobjects.ini*:'''
    Konfiguration für den Service der Page-Dekoratoren. Selbst
    implementierte Dekoratoren müssen hier hinzugefügt werden. Der
    Service-Name entspricht dem Dekorator-Type, ist aber am Anfang immer
    groß geschrieben (Upper-CamelCase).

Eine beispielhafte Installation ist in der folgenden Grafik dargestellt:
[<http://forum.adventure-php-framework.org/de/download/file.php?id=171>](/http://forum.adventure-php-framework.org/de/download/file.php?id=171 "wikilink")
Der Kontext ist hier "sites", das Environment "DEFAULT".

<div style="background: #ff8; border: 2px solid #fb3; color: #333; margin: 1em 0 2em 0; min-height: 2em; padding: 8px; font-weight:bold;">

Wer das APFelSMS aus dem APF-Repository (per SVN) bezogen hat, findet
diese Konfigurationsdateien in *apps/config/extensions/apfelsms/*. Sie
werden später im APF-Configpack enthalten sein, und *nicht* im
Extension-Verzeichnis beiliegen.

Im SVN-Repo finden sie die Configs für die Version v1.0 derzeit hier:
<http://sourceforge.net/p/adventurephpfra/code/HEAD/tree/branches/php5/2.0/config/extensions/apfelsms/>

</div>

## Schritt 2: Einstellungen anpassen

In
*config\\extensions\\apfelsms\\{CONTEXT}\\{ENVIRONMENT}_serviceobjects.ini*
müssen nun noch folgende Einstellungen vorgenommen werden:

``` ini
[Manager]
; ...
conf.requestParam.method = "setPageRequestParamName"
conf.requestParam.value = "{REQUEST_PARAM_NAME}"
; ...

[Site]
; ...
conf.title.method = "setWebsiteTitle"
conf.title.value = "{WEBSITE_TITLE}"
conf.start.method = "setStartPageId"
conf.start.value = "{START_PAGE_ID}"
conf.404.method = "set404PageId"
conf.404.value = "{404_PAGE_ID}"
conf.403.method = "set403PageId"
conf.403.value = "{403_PAGEID}"
; ...

[Mapper]
; ...
conf.file.method = "setXMLFilename"
conf.file.value = "{XML_FILE}"
conf.namespace.method = "setXMLFileNamespace"
conf.namespace.value = "{XML_FILE_NAMESPACE}"
; ...
```

-   **{REQUEST_PARAM_NAME}:** Der Name des Request-Parameters, in dem
    die Seiten-ID übermittelt wird. Standard ist "page". Eine URL dazu
    wäre z.B. *<http://example.com/?page=about>*
-   **{WEBSITE_TITLE}:** Name der Website. Wird von der Erweiterung
    u.a. bei der Erzeugung von
    <title>

    -Tags genutzt.
-   **{START_PAGE_ID}:** Seiten-ID der Startseite. Diese ID wird z.B.
    verwendet, wenn keine Seiten-ID angegeben ist. Ist hier keine ID
    angegeben oder die ID nicht definiert, führt das zwangsweise zu
    Fehlern.
-   **{404_PAGE_ID}, {403_PAGE_ID}:** Seiten-IDs der Fehlerseiten.
    Auch diese Seiten-IDs müssen unbedingt angegeben und fehlerfrei
    sein!
-   **{XML_FILE}, {XML_FILE_NAMESPACE}:** Name und Namespace der
    XML-Datei mit der Seitenstruktur, z.B. *apfelsms.xml* und
    *APF\\extensions\\apfelsms\\data\\* (Nur auf den XML-Mapper
    anwendbar.)

Die mitgelieferte Konfiguration ist bereits ausgefüllt und muss nur ggf.
angepasst werden.

# Einrichtung

## Seitenbaum anlegen

In der vorliegenden Version (v1.0) liegt nur ein XML-Mapper bei. Die
Seitenstruktur muss demnach in einer XML-Datei abgelegt werden und
entspricht im wesentlichen dem DOM der XML-Datei. Folgende Tags stehen
dazu zur Verfügung:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE apfelsms SYSTEM "apfelsms.dtd">

<apfelsms>
<page id="{PAGE-ID}">
   <title>{SEITENTITEL}</title>
   <navTitle>{ALTERNATIVER NAVIGATIONSTITEL}</navTitle>
   <css>{STYLESHEET-URL}</css>
   <js>{JAVASCRIPT-URL}</js>
   <pageDec type="{DEKORATOR-TYP}">
      ... Eigenschaften ...
   </pageDec>
   <page id="...">
      ... Eigenschaften und Dekoratoren der Unterseite...
   </page>
   ... weitere Unterseiten ...
</page>
</apfelsms>
```

-   **<apfelsms>:** Document-Root Element der XML-Datei. (Keine weitere
    Funktion)
-   **<page id="...">:** Container-Element für eine Seite. Innerhalb
    dieses Tags werden alle Tags für Seiteneigenschaften, -dekoratoren
    und Unterseiten angelegt. Das Attribut id ist Pflicht und muss eine
    **einmalige** Seiten-ID als Wert haben.
-   '''
    <title>

    :''' Der Titel der Seite. Im Normalfall ist dies ein Pflicht-Tag.
    Nur bei Seiten mit referenzierenden Dekoratoren (z.B. Dekorator-Typ
    "Alias") kann er ggf. entfallen.
-   **<navTitle>:** Ein optionaler, alternativer Navigationstitel. Als
    Beispiel für eine Anwendung stelle man sich eine Hauptseite mit
    Titel "Herzlich Willkommen!" und NavTitel "Home" vor. Als
    Seitentitel steht dort dann "Herzlich Willkommen!", in der
    Navigation und in erzeugten Links "Home". Wird keiner angegeben,
    wird **title** verwendet, wo der Navigationstitel verwendet werden
    soll.
-   **<css>:** Dieses Tag ist optional und kann mehrfach verwendet
    werden, um Stylesheets für die aktuelle Seite zu definieren. Mit dem
    optionalen Attribut "key" lässt sich der Medientyp für das
    Stylesheet angeben, jedoch ist pro Wert des Attributs nur ein
    Stylesheet definierbar. Um die Stylesheets auch wirklich
    einzubinden, muss die Taglib *<sms:cssIncludes />* verwendet werden!
-   **<js>:** Ebenfalls ein optionales, mehrfach verwendbares Tag zur
    Einbindung von Javascripts. Um die Stylesheets auch wirklich
    einzubinden, muss die Taglib *<sms:jsIncludes />* verwendet werden!
-   **<pageDec type="...">:** Fügt der Seite einen Dekorator hinzu. Mehr
    zu den Dekorator-Typen und ihren Eigenschaften im nächsten Kapitel.

<div style="background: #ff8; border: 2px solid #fb3; color: #333; margin: 1em 0 2em 0; min-height: 2em; padding: 8px;">

Damit der Mapper die Seiten-IDs erkennen kann, ist die Einbindung der
beiliegenden DTD (DocumentTypeDefinition) zwingend erforderlich.
Eigenschaften selbst implementierter Dekoratoren müssen darin ergänzt
werden. Einige IDEs können durch die DTD zudem die Eingabe unterstützen
und Fehler erkennen.

</div>

## Dekoratoren verwenden

Mittels der <pageDec>-Tags lassen sich den Seiten innerhalb der
XML-Datei Dekoratoren hinzufügen. Folgende Dekoratortypen mit ihren
Eigenschaften werden in der vorliegenden Version (v1.0) mitgeliefert:

-   **hidden:** Hat keine Eigenschaften und versteckt die Seite in
    Navigationen (mit Ausnahme der Brotkrümmelnavigationen).
-   **alias:** Stellt eine Referenz auf eine andere Seite her. Dies
    umfasst (sofern nicht für die dekorierte Seite angegeben) Titel und
    Navigationstitel, eventuellen Zugriffsschutz und die Verwendung des
    Templates der referenzierten Seite. Nicht referenziert werden
    Unterseiten, der Dekorator nimmt also keinen Einfluss auf die
    Seitenstruktur. Die referenzierte Seite wird mit dem Parameter
    **referencedPageId** angegeben.
-   **redirect:** Verhält sich genauso wie der *alias*-Dekorator,
    verlinkt allerdings auf die referenzierte Seite. Es wird eine
    FC-Action mitgeliefert, die bei Aufruf einer Seite mit diesem
    Dekorator auf die referenzierte Seite umleitet oder den
    Request-Parameter für die Seiten-ID anpasst.
-   **externalURL:** Manipuliert die URL von Links auf die Seite und
    liefert stattdessen die im Parameter **externalURL** angegebene URL
    zurück. Es wird eine FC-Action mitgeliefert, die bei Aufruf einer
    Seite mit diesem Dekorator auf die externe URL umleitet, sofern sich
    Host oder Pfad gegenüber der aktuellen URL unterscheiden.
-   **request:** Fügt der URL der Seite zusätzliche Request-Parameter
    hinzu. Es können mehrere Parameter mit **requestParam** angegeben
    werden. Das Attribut "key" gibt den Namen des Parameters an.
-   **parentRequest:** So wie *request*, jedoch kombiniert mit *alias*
    mit Referenz auf die übergeordnete Seite.
-   **anchor:** Fügt der URL einen Anker (Sprungmarke, z.B.
    "*\#kapitel-2*") hinzu. Die Sprungmarke wird mit dem Parameter
    **anchor** angegeben.
-   **parentAnchor:** So wie *anchor*, jedoch kombiniert mit *alias* mit
    Referenz auf die übergeordnete Seite.
-   **accessCtrl:** Mit diesem Dekorator lässt sich der Zugriff auf eine
    Seite kontrollieren. Die Information, ob eine Seite
    zugriffsgeschützt ist, wird von einem *Access-Control Provider*
    bezogen. Er wird als DIService bezogen. Mit den Parametern
    **providerServiceName** und **providerServiceNamespace** (default
    ist der Namespace
    *APF\\extensions\\apfelsms\\biz\\pages\\decorators\\provider*) wird
    der zu verwende Provider festgelegt. Über einen weiteren Parameter
    **permissionName** lässt sich dem Provider übermitteln, welche
    Berechtigung ausschlaggebend ist (default ist *SMSViewPermission*).
    Weitere Informationen zu den *Access-Control Providern* im
    Allgemeinen und den beiliegenden Providern für das
    Usermanagement-Modul finden sich weiter unten.
-   **loggedIn:** Der Dekorator *SMSUmgtLoggedInPageDec* regelt die
    Anzeige (Attribut "hidden") einer Seite in Abhängigkeit, ob im
    Usermanagement-Modul ein Benutzer angemeldet ist. Dies stellt keinen
    Zugriffsschutz dar! Ohne weitere Konfiguration wird die Seite
    versteckt, wenn kein Benutzer angemeldet ist. Über die Methode
    *setHiddenOnLogin(true)* lässt sich dieses Verhalten auch
    invertieren: Die Seite wird angezeigt, wenn KEIN Benutzer angemeldet
    ist. Beide Varianten sind über die beiliegende Service-Konfiguration
    als Dekorator-Typen *loggedIn* und *notLoggedIn* verfügbar.

### Access-Control Provider / Zugriffsschutz

Mit dem **AccessCtrl** Dekorator lässt sich der Zugriff auf Seiten
regulieren. Er verwendet dazu **Access-Control Provider**. Für das
Usermanagement-Modul des APF liegen einige Provider bei. Provider können
selbstverständlich auch entsprechend der eigenen individuellen
Anforderungen entwickelt werden. Der Provider muss dazu dass Interface
*SMSAccessCtrlProvider* im Namespace
*APF\\extensions\\apfelsms\\biz\\pages\\decorators\\provider*
implementieren.

Folgende Provider liegen dem APFelSMS ab Version 0.4 bei:

-   **UmgtAccessCtrlProvider:** Regelt den Zugriff anhand der
    Berechtigungen (*Permission*) des Benutzers sowie seiner Gruppen und
    Rollen, die mit dem *permissionName*-Parameter des
    *AccessCtrl*-Dekorators angegeben werden. Mit der Methode
    *setAnonymousAccess()* kann (z.B. über DI-Service Konfiguration) der
    anonyme Zugang erlaubt werden.
-   **UmgtVisibilityAccessCtrlProvider:** Regelt den Zugriff anhand von
    Sichtbarkeitsbedingungen (*AppProxy* bzw. *VisibilityDefinition*)
    des Benutzers und seiner Gruppen. Die *AppProxyID* (siehe
    Dokumentation des UMGT-Moduls) entspricht der ID der Seite. Mit dem
    *permissionName*-Attribut wird der *AppProxyType* angegeben. Mit der
    Methode *setAnonymousAccess()* kann (z.B. über DI-Service
    Konfiguration) der anonyme Zugang erlaubt werden. Diese Variante
    wird statt des *UmgtAccessControlProvider* deutlich empfohlen!
-   **UmgtLoggedInCtrlProvider** und **UmgtNotLoggedInCtrlProvider:**
    Regeln den Zugang je nachdem ob ein bzw. ob kein Benutzer angemeldet
    ist. Benötigt kein *permissionName*.

## Beispiel einer Seitenstruktur

Hier ein Beispiel für eine Seitenstruktur und die Verwendung der
Dekoratoren:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE apfelsms SYSTEM "apfelsms.dtd">
<apfelsms>


   <!-- Main navigation and page tree -->
   <page id="index">
      <title>Herzlich Willkommen!</title>
      <navTitle>Home</navTitle>
   </page>
   <page id="news">
      <title>Aktuelles</title>
      <page id="news-archive">
         <title>News-Archiv</title>
         <pageDec type="alias"><referencedPageId>news</referencedPageId></pageDec>
         <pageDec type="request"><requestParam key="archive">show</requestParam></pageDec>
      </page>
   </page>
   <page id="about">
      <title>Über uns</title>
   </page>
   <page id="contact">
      <title>Kontakt</title>
   </page>
   <page id="impress">
      <title>Impressum & Disclaimer</title>
      <navTitle>Impressum</navTitle>
   </page>


   <!-- Pages which does not occur in the navigation -->
   <page id="notFound">
      <title>404 - Nicht gefunden!</title>
      <pageDec type="hidden" />
   </page>


   <!-- Access protected admin portal -->
   <page id="admin">
      <title>Adminportal</title>
      <navTitle>Admin</navTitle>
      <pageDec type="loggedInOrHidden" />

      <page id="admin-news">
         <title>Newsadmin</title>
         <navTitle>News</navTitle>
         <pageDec type="accessCtrl">
            <providerServiceNamespace>APF\extensions\apfelsms\pages\decorators\provider</providerServiceNamespace>
            <providerServiceName>UmgtAccessCtrlProvider</providerServiceName>
            <permissionName>news</permissionName>
         </pageDec>
      </page>
      <page id="admin-dates">
         <title>Dateadmin</title>
         <navTitle>Dates</navTitle>
         <pageDec type="accessCtrl">
            <providerServiceNamespace>APF\extensions\apfelsms\pages\decorators\provider</providerServiceNamespace>
            <providerServiceName>UmgtAccessCtrlProvider</providerServiceName>
            <permissionName>date</permissionName>
         </pageDec>
      </page>
   </page>



   <!-- Additional navigation tree -->
   <page id="footer-nav">
      <pageDec type="hidden" />

      <page id="footer-home">
         <pageDec type="redirect">
            <referencedPageId>home</referencedPageId>
         </pageDec>
      </page>
      <page id="footer-contact">
         <pageDec type="redirect">
            <referencedPageId>contact</referencedPageId>
         </pageDec>
      </page>
      <page id="footer-impress">
         <pageDec type="redirect">
            <referencedPageId>impress</referencedPageId>
         </pageDec>
      </page>
   </page>

</apfelsms>
```

# Taglibs

Die Verwendung des APFelSMS erfolgt im wesentlichen mittels Taglibs, die
im Folgenden vorgestellt werden. Die Taglibs liegen im Namespace
*APF\\extensions\\apfelsms\\pres\\taglibs* und müssen zunächst
eingebunden werden. Hierzu steht eine eigene Taglib zur Verfügung, die
die Einbindung aller anderen Taglibs des APFelSMS übernimmt:

``` xml
<core:addtaglib prefix="sms" name="addAll" class="APF\extensions\apfelsms\pres\taglibs\SMSAddAllTag"/><sms:addAll/>
```

## Eigenschaften der Seite einbinden

=== Seiten- und Websitetitel und HTML

<title>

-Tag === Um ein

<title>

-Tag zu erzeugen muss lediglich ein Aufruf von **<sms:title />**
erfolgen. Der Titel wird wie üblich zusammengebaut: *\[Seitentitel\] -
\[Webseitentitel\]*. Analog können die Taglibs **<sms:pageTitle />** und
**<sms:siteTitle />** verwendet werden, um nur den aktuellen Seitentitel
bzw. den Titel der Website ausgeben zu lassen. **<sms:pageTitle />**
kennt ein optionales Attribut *pageId*, mit dem man die ID der Seite
angeben kann, deren Titel ausgegeben wird. Standard ist die aktuelle
Seite.

### CSS- und JS-Dateien einbinden

Die Taglibs **<sms:cssIncludes />** und **<sms:jsIncludes />** binden
die im Seitenbaum angegebenen Stylesheets und Scripts ein. Sie haben
keine Parameter. **<sms:cssIncludes />** erzeugt <link>-Tags, weshalb
eine Verwendung nur im HTML-Header ratsam ist. Die Javascripts sollten
hingegen möglichst am Ende des HTML-Dokuments eingebunden werden, damit
das Laden der Scripte den Aufbau der Seite nicht verzögert. Beide
Taglibs haben keine Parameter.

## Navigation erzeugen

### Standard-Navigationen

Navigationen lassen sich mit **<sms:nav />** erzeugen. Diese Taglib
kennt einige Parameter, mit denen sich die Ausgabe beeinflussen lässt:

-   **basePageId:** Gibt die Seiten-ID an, die den darzustellenden
    Seitenbaum beherbergt bzw. die im darzustellenden Seitenbaum
    enthalten ist. Ist keine ID angegeben, wird von der aktuellen Seite
    ausgegangen.
-   **level:** Von der basePageId ausgehend wird auf das angegebene
    Level (Ebene) relativ zum gesamten Baum gesprungen. Das Level 0 ist
    dabei die oberste Ebene, Level 1 wären alle Unterseiten der obersten
    Ebene usw.. Das Attribut level gibt an, ab welcher Ebene die
    Navigation ausgegeben wird.
-   **rellevel:** Wird für rellevel der Wert *true* angegeben, wird die
    Ebene der Navigation nicht relativ zum gesamten Seitenbaum, sondern
    ausgehend von der basePageId berechnet.
-   **depth:** Gibt an, wie viele Ebenen tief Unterseiten in der
    Navigation mit ausgegeben werden. Der Wert *auto* lässt alle
    Unterseiten im aktuellen Zweig bis zur Ebene der aktuellen Seite
    plus eine Ebene drunter ausgeben, sodass die aktuelle Seite und ihre
    Kinder sichtbar sind.
-   **template** und **namespace:** Mit diesen beiden Parametern lässt
    sich ein alternatives Template angeben, um die HTML-Struktur der
    Ausgabe anzupassen. Also Vorlage für eigene Templates sollte das
    beiliegende *navTaglib.html* in
    *APF\\extensions\\apfelsms\\pres\\templates* dienen. Insbesondere
    ist darauf zu achten, dass der angegebene Documentcontroller
    eingebunden wird und alle von ihm erwarten Placeholder verwendet
    werden!
-   **keepRequestParams:** Hier lässt sich eine kommasepartierte Liste
    von GET-Parametern angeben, die aus der aktuellen URL in die Links
    übernommen werden sollen. Mit dem Wert *__SMS-ALL__* lassen sich
    alle Parameter erhalten. Der Parameter ist optional.

### Brotkrümmel-Navigationen

[Brotkrümmel-Navigationen](http://de.wikipedia.org/wiki/Brotkr%C3%BCmelnavigation#Location_.28Ort.29)
lassen sich mit **<sms:breadcrumbNav />** erzeugen. Die Taglib kennt
analog zur Taglib *<sms:nav />* die Parameter *template* und *namespace*
für ein angepasstes Template. Die Hinweise gelten selbstverständlich
ebenfalls. Die Taglib kennt zudem folgende Parameter:

-   **basePageId:** Seiten oberhalb der angegebenen ID werden in der
    Brotkrümmel-Navigation nicht dargestellt. Seiten auf der selben
    Ebene werden als Unterseiten dargestellt. Die basePageId wird immer
    als oberstes Element dargestellt. Ist keine basePageId angegeben,
    wird die definierte Startseiten-ID verwendet.
-   **basePageIdTitle:** Ein alternativer Seitentitel, der für die
    basePage verwendet wird.
-   **keepRequestParams:** Hier lässt sich eine kommasepartierte Liste
    von GET-Parametern angeben, die aus der aktuellen URL in die Links
    übernommen werden sollen. Mit dem Wert *__SMS-ALL__* lassen sich
    alle GET-Parameter erhalten. Der Parameter ist optional.

### Beispiel des Einsatzes der Navigations-Taglibs

``` xml
<body>
   <div id="header">
      <h1><sms:siteTitle /></h1>
      <div id="main-nav">
         <sms:nav basePageId="index" level="0" depth="1" keepRequestParams="benchmark,theme" />
      </div>
   </div>
   <div id="content">
      <div id="breadcrumNav"><sms:breadcrumbNav basePageId="index" basePageIdTitle="Startseite" /></div>
      <h2><sms:pageTitle /></h2>

      ...

   </div>
   <div id="left">
      <div id="subnav">
         <sms:nav basePageId="index" level="1" depth="auto" />
      </div>

      ...

   </div>
</body>
```

## Templates einbinden

Mit der Taglib **<sms:importdesign />** lassen sich Templates anhand der
aktuellen oder einer angegebenen Seiten-ID einbinden. Der Vorteil
gegenüber *core:importdesign /&gt;* liegt darin, dass mittels
Dekoratoren das eingebundene Template beeinflusst und der Zugriffsschutz
durch Dekoratoren berücksichtigt wird. Außerdem wird im Fehlerfall
automatisch auf die angegebenen Seiten für 404- und 403-Fehler
zurückgegriffen, sofern keine anderen Template-Namen für diesen Fall
angegeben wurden. Der Template-Name ist im Regelfall die Seiten-ID, es
sei denn, er wird durch Dekoratoren beeinflusst. Die Taglib bezieht den
Template-Namen über die Methode *SMSPage::getTemplateName()*.

-   **namespace:** Gibt an, in welchem Namespace sich das einzubindende
    Template befindet.
-   **pageId:** ID der Seite, deren Template eingebunden wird. Wenn
    nicht angegeben, wird die aktuelle Seite verwendet.
-   **notFoundTemplate** und **notAllowedTemplate:** Template-Namen für
    404- und 403-Fehler. Wenn nicht angegeben, werden die Seiten-IDs für
    die Fehlerseiten aus der Konfiguration als Template-Namen verwendet.
    404-Fehler treten auf, wenn ein Template im angegebenen Namespace
    nicht gefunden wird; 403-Fehler, wenn die gewünschte Seite durch
    einen Dekorator als geschützt makiert wird.

## Links zu Seiten erzeugen

Links lassen sich mit der Taglib
**<sms:pageLink>{Link-Text}</sms:pageLink>** erzeugen. Neben Links zu
konkreten Seiten-IDs beherrscht die Taglib auch einige *magische IDs*.
Als Link-Text wird der Inhalt der Taglib verwendet. Ist die Taglib ein
leeres Element (hat keinen Inhalt), wird der Navigationstitel verwendet.

Diese Taglib gibt alle Attribute, die bei einem a-Tag in HTML möglich
sind, an das erzeugte a-Tag weiter. Das title-Attribut wird automatisch
mit dem Seitentitel gefüllt, sofern nicht angegeben. In früheren
Versionen wurde das Attribut **id** auch für die Seiten-ID verwendet.
Dies wird noch unterstützt, die Verwendung des pid-Attributs ist aber
empfohlen.

-   **anchor:** Fügt dem Link einen Anker (Sprungmarke, z.B.
    "*\#kapitel-2*") hinzu.
-   **pid:** Gibt an, zu welcher Seite verlinkt werden soll. Akzeptiert
    werden konkrete Seiten IDs und die folgenden 'magischen IDs'':

'' **Magische Seiten-IDs:** ''

-   **__current:** Link zur aktuellen Seite. *Alle Request-Parameter
    der aktuellen URL werden entfernt. Es werden nur der
    Seiten-Parameter und Parameter, die durch Dekoratoren hinzugefügt
    werden, der Link-URL hinzugefügt. (Gilt auch für alle anderen
    konkreten und magischen IDs).*
-   **__referer:** Link zur zuvor aufgerufenen Seite. Enthält die
    Referer-URL keine valide Seiten-ID, ist nicht definiert oder stammt
    von einem anderen Host *(ab v0.3-RC2)*, wird zur aktuellen Seite
    verlinkt.
-   **__start:** Wird durch die ID der Startseite ersetzt.
-   **__parent:** Verlinkt zur übergeordneten Seite. Gibt es keine
    übergeordnete Seite, wird die Startseite verwendet.
-   **__prev** und **__next:** Erzeugen einen Link zur in der
    Seitenstruktur vorher bzw. nachher angegebenen Seite (linke bzw.
    rechte Geschwister). Gibt es die entsprechenden Geschwister nicht,
    gibt die Taglib nichts (einen leeren String) zurück.

# Frontcontroller-Actions

## Konfiguration

Um die beiliegenden Frontcontroller-Actions nutzen zu können, muss
zunächst die in *config* beiliegende *actionconfig.ini* in den
*config*-Namespace kopiert und umbenannt werden. Beim Kontext *"sites"*
und Environment *"DEFAULT"* wäre das der Pfad
*config/extensions/apfelsms/sites/DEFAULT_actionconfig.ini*.

Dann müssen die Actions nur noch dem Frontcontroller hinzugefügt werden
und dieser gestartet werden. Für das obige Beispiel sähe das so aus:

``` php
<?php
include_once './apps/core/bootstrap.php';


use \APF\core\singleton\Singleton;
use \APF\core\frontcontroller\Frontcontroller;

/** @var $fC Frontcontroller */
$fC = &Singleton::getInstance('APF\core\frontcontroller\Frontcontroller');
$fC->setContext('sites');
$fC->setLanguage('de');

// add static actions
$fC->addAction('APF\extensions\apfelsms', 'CurrentPageCheck');
$fC->addAction('APF\extensions\apfelsms', 'Redirect');
$fC->addAction('APF\extensions\apfelsms', 'ExternalURLRedirect');

echo $fC->start('{MEIN_NAMESPACE}', '{MEIN_TEMPLATE}');
```

## Aktuelle Seite auf Fehler prüfen und HTTP-Status anpassen

Um bei einer fehlerhaften Seiten-ID oder geschützten Seite direkt auf
der entsprechenden Fehlerseite zu landen gibt es die
**CurrentPageCheck**-Action im Action-Namespace
*APF\\extensions\\apfelsms*. Außerdem passt diese Action den
HTTP-Status-Code der Fehlerseiten an. Eine 404-Fehlerseite bekommt so
auch den passenden Status-Code, der bspw. von Suchmaschinen-Bots erkannt
wird (und die entsprechende Seite nicht indexiert).

## Weiterleitung beim Aufruf von Seiten mit Redirect- oder ExternalURL-Dekorator

Die beiden Actions **Redirect** und **ExternalURLRedirect** erweitern
die Funktion der Dekoratoren *redirect* und *externalURL* und leiten auf
die referenzierte Seite weiter, falls eine Seite mit diesen Dekoratoren
aufgerufen wird.

Ein Beispiel:

``` xml
<page id="news">
   <title>Aktuelles</title>
</page>

<!-- ... -->

<page id="footer-news">
   <pageDec type="redirect">
      <referencedPageId>news</referencedPageId>
   </pageDec>
</page>
```

Würde man nun bspw. die URL *www.example.com/page/footer-news* aufrufen,
dann leitet die Action auf die eigentliche Seite
*www.example.com/page/news* weiter.

# FAQ (Frequently Asked Questions)

### Welche APFelSMS-Version habe ich?

**A:** Die Methode *SMSManager::getSMSVersion()* verrät dir die Antwort.

### Wie kann ich eigene Dekoratoren erstellen ?

**A:** Dazu musst du eine neue Klasse definieren, die von
*SMSAbstractPageDec* im Namespace
*APF\\extensions\\apfelsms\\biz\\pages\\decorators* erbt. In welchem
Namespace du diese neue Klasse ablegst, ist egal. Gemäß der
Namenskonvention des APFelSMS sollte sie auf "PageDec" enden. Möchtest
du sie zum APFelSMS beisteuern, sollte sie mit "SMS" beginnen. Also z.B.
*SMSMyAwesomePageDec*. In dieser Klasse kannst du neue Methoden
definieren oder die Methoden aus dem Interfaces *SMSPage* überschreiben.
Um sie zu verwenden, musst du deiner Servicekonfiguration im Namespace
*APF\\config\\extensions\\apfelsms\\pages\\decorators\\{CONTEXT}* (oder
ähnlich) einen neuen Service hinzufügen. Der Service-Name entspricht dem
Dekorator-Typ, wird aber am Anfang immer groß geschrieben. Für unser
Beispiel könnte das so aussehen:

``` ini
; ... vorhandene Dekorator-Services

[MyAwesome]
class = "APF\my\namespace\SMSMyAwesomePageDec"
servicetype = "NORMAL"
```

### Parameter mit dem Mapper auslesen

**F:** Ich habe eine neue Implementierung einer Seite oder eines
Dekorators für meine APFelSMS-Anwendung geschrieben und möchte Parameter
aus der Datenschicht mit dem Mapper auslesen. Wie teile ich dem Mapper
mit, was er mappen soll?

**A:** Der Mapper liest die statische Eigenschaft *$mapVars* des zu
mappenden Objekts aus. Diese stellt ein Array mit assoziativen
Schlüsseln dar. Die Schlüssel sind die Namen der Eigenschaften des
Objekts (bei XML-Dateien zugleich der Tag-Name). Ausnahme: Bei
Objekteigenschaften, die als Array gemappt werden, *kann* die
Objekteigenschaft mit einem Plural-"s" enden, während der Name der
Eigenschaft kein Plural-"s" hat.

Die Werte des *$mapVars*-Arrays werden übernommen, wenn kein Wert aus
der Datenschicht ausgelesen wird. Wird *null* als Wert angegeben, wirft
der Mapper eine Exception, wenn er keinen Wert mappen kann.

Ist der Wert in *$mapVars* ein Array, akzeptiert der Mapper mehrere
Werte, die er dann auch als Array zurück gibt. Beim XML-Mapper
entspricht das mehreren gleichnamigen Tags. Die Schlüssel des
zurückgegebenen Arrays für diese Eigenschaft können hier durch das
Tag-Attribut "key" beeinflusst werden.

Der Mapper injiziert die Werte automatisch in die Eigenschaften des
Objekts mittels der Methode *mapData()*, die im Normalfall vererbt
werden sollte, und nicht selbst implementiert werden muss. Seiten
sollten dazu von *SMSStdPage* erben, Dekoratoren von
*SMSAbstractPageDec*.

<div style="background: #ff8; border: 2px solid #fb3; color: #333; margin: 1em 0 2em 0; min-height: 2em; padding: 8px;">

Beachtet werden muss, dass die Eigenschaften der vererbenden Klassen
(Eltern-Klassen) in *$mapVars* beim Überschreiben der Eigenschaft wieder
aufgenommen werden!

</div>

**Beispiel (anhand von *SMSStdPage*):**

``` php
   /**
    * @var array
    */
   public static $mapVars = array(
      'title' => '',
      'navTitle' => '',
      'js' => array(),
      'css' => array()
   );
```

``` xml
<page id="example">
   <title>Ein Beispiel</title>
   <js>script.js</js>
   <css>index.css</css>
   <css key="print">print.css</css>
</page>
```

# Roadmap

-   Vererbung von ausgewählten Eigenschaften und Dekoratoren an
    Unterseiten
-   Erweiterung der Taglibs
-   Eine Description-Eigenschaft für Seiten, die im title-Attribut eines
    Anchors verwendet werden kann.
-   Mapper für eine Datenspeicherung in Datenbanken. Wenn möglich, soll
    dabei das NestedSets-Pattern verwendet werden.
-   Mehrsprachigkeit für die Taglib-Attribute **basePageIdTitle** und
    **title**