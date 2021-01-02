Mit Version 2.0 wurde die Verwaltung der Roadmap in ein BugTracker
überführt. Alle Einträge von Version 2.0 und aus dem Backlog sind in den
Tracker überführt worden (Stand 21.05.2013). Bitte in Zukunft den
BugTracker nutzen. Der Tracker ist erreichbar unter
<http://tracker.adventure-php-framework.org>

Die Roadmap beschreibt den Funktionsumfang der zukünftigen Versionen des
Frameworks. Sie basiert jeweils auf der aktuellen Planung des
Entwickler-Teams, kann jedoch durch Einstreuen von weiteren
Anforderungen beeinflusst werden. Hierzu eignet sich insbesondere das
[Neue
Features](http://forum.adventure-php-framework.org/de/viewforum.php?f=10)-Forum

**Der Release-Plan für die zukünftigen Versionen gestaltet sich wie
folgt:**

## Backlog

Die hier aufgeführten Features sind noch keinem Release zugeordnet. Wenn
Sie Interesse an der Umsetzung haben, melden Sie sich doch im
[Forum](http://forum.adventure-php-framework.org/) und übernehmen Sie
die Entwicklung und Dokumentation des Features.

-   Invalide Formulare sollen beim Absenden bereits hochgeladene Dateien
    zwischen speichern können (siehe
    [1](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=893)).
-   Es können verschiedene Einstieg-Templates definiert werden. Anhand
    vom Content-Type-Header wird eines dieser Templates selektiert
    (siehe
    [2](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&p=11158#p11151)).
-   Erweiterung Filter- und Validator-Config (Proposal siehe
    [Erweiterung Filter- und Validator-Config (Release
    1.12)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=184))
-   Um das neue Gästebuch auch in eigenen Applikationen als vollwertiges
    Modul einsetzen zu können, soll die Datenschicht um eine
    Lösch-Funktion erweitert werden.
-   Refactoring Datenbank-Connection-Layer. Ziel:
    -   Vereinheitlich des Interface
    -   Komplette Unterstützung Prepared Statements
    -   Umstellung Connection-Erzeugung auf DIServiceManager (?)
-   Datenbankabstraktionsschicht
-   **Überarbeitung des GenericORMappers** (siehe [1.17
    GORM](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=1262))
    -   Evaluation von Datenbank-übergreifenden Statements mit dem GORM
        (Diskussion siehe [GORM - Beziehungen über mehrere
        Datenbanken](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=369)).
    -   Erweiterung des Update-Tools des GORM um das erweiterte
        Index-Handling (Diskussion siehe
        [Indices](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=466)).
    -   Transaktions-Handling für den GORM (Proposal siehe
        [Transaktions-Handling für den GORM (Release
        1.12)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=220))
    -   Erweiterung GORM-Konfiguration zur Definition von
        Storage-Engines pro Tabelle (Setup- und Update-Tool erweitern!)
    -   Mehrsprachigkeit out-of-the-box mit dem GORM? (siehe
        [3](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=842#p9048)).
    -   Es soll im GORM möglich sein, Qualitäten von Beziehungen (1:1,
        1:n, n:1, n:m) zu definieren und vom GORM prüfen zu lassen
        (Proposal siehe Erweiterung Beziehungs-Management GORM).
    -   Erweiterung der Methode loadObjectCount damit der
        addRelationIndicator im GenericCriterionObject möglich ist.
    -   Erstellen einer GUI für die Erstellung und Aktualisierung von
        GORM-basierten Datenbanken (Diskussion siehe [GORM Update-Tool
        löscht noch benötigte
        Tabellen!](http://forum.adventure-php-framework.org/viewtopic.php?f=8&t=1380#p13433)
-   HTML5 Unterstützung für Formulare (neue Formularelemente,
    Validierung, Clientvalidierung nur als Fallback wenn möglich)
    (Zusammenfassung siehe
    [4](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=664&start=60#p9265)).
-   Data-Bindings für Formulare ([Proposal für
    Data-Bindings](/Proposal_für_Data-Bindings "wikilink"))

## Version 2.0

Nach dem Release 1.17 wird die nächste Version des APF die Version 2.0
tragen. Dies ergibt sich aus der bisherigen Feature-Liste, die
ursprünglich für 1.18 geplant war (z.B. Umstellung auf Namespaces).

Die Ideen für eine Version 2.0 werden auf der Seite [Features APF
2.0](/Features_APF_2.0 "wikilink") beschrieben und im Forum unter [2.xx
Ideensammlung](http://forum.adventure-php-framework.org/viewtopic.php?f=10&t=340)
diskutiert.

-   **\[Erledigt\]** Integration PHP-Namespaces in das APF:
    -   Explizite Definition von Namespaces pro Datei
    -   Anpassen Klassen-Signaturen auf Namespace + Klasse (z.B.
        *Singleton*)
    -   Refactoring *import()* auf *use*
-   Erzeugung von Document-Controllern über den DI-Container "on-demand"
    (=sofern gewünscht).
-   Erzeugung von Front-Controller-Actions über den DI-Container
    "on-demand" (=sofern gewünscht).
-   Einführung einer Objekt-Erzeugung im *APPLICATION_SINGLETON*-Modus
    analog zu *SESSION_SINGLETON* jedoch über APF für die gesamte
    Applikation.
-   Unterstützung von TTL-Definitionen pro *CacheKey*-Instanz zusätzlich
    zur Konfiguration pro *CacheProvider* (Dokumentation + Refactoring
    bestehender Provider; *ApcCacheProvider* unterstützt dies im Code
    bereits ab 1.17).
-   Erweiterung *Url* und *LinkScheme*-Implementierungen um
    Anchors/Hashes zu unterstützten (Details siehe
    [5](http://forum.adventure-php-framework.org/viewtopic.php?f=10&t=1420)).
-   **\[Erledigt\]** Um die Nutzung des *<html:template />*-Tags zu
    vereinfachen soll der *TemplateTag* mit einer Methode *clear()*
    ausgestattet werden um nach einem Schleifendurchlauf alle
    Platzhalter zurücksetzen zu können.
-   Umbenennung von *SessionManager* zu *Session*, da der
    *SessionManager* lediglich eine Abstraktion der Session und keine
    Business-Komponente darstellt.

## Version 1.17

In der Version 1.17 sollen nach aktuellem Stand folgende Features
entwickelt werden (draft):

-   **\[Erledigt\]** Refactoring Klassen-Namen basierend auf neuer
    Tag-Parser-Logik (Klassen-Namen sind seit 1.16 unabhängig von
    Tag-Präfix und -Namen). Z.B.:
    -   base_controller -&gt; BaseDocumentController
    -   html_taglib_placeholder -&gt; HtmlPlaceHolderTag
    -   html_taglib_addtaglib -&gt; AddTag(Tag)
-   Implementierung eines **TimeBasedCacheProvider** für
    *SimpleCacheKey* und *AdvancedCacheKey*.
-   **\[Erledigt\]** Implementierung eines APC *CacheProvider* um
    Inhalte Request-übergreifend im RAM-Cache speichern zu können.
-   **\[Erledigt\]** Implementierung eines APC *ConfigurationProvider*
    um Konfigurationen Request-übergreifend im RAM-Cache zwischen
    speichern zu können (Pattern: *MemcachedConfigurationProvider*).
-   **\[Erledigt\]** Bis einschließlich Release 1.16 unterstützt der
    APF-Parser die Verarbeitung von verschachtelten Strukturen von Tags
    mit gleichem Tag-Präfix und -Namen nicht. Im Release 1.17 soll der
    Parser um die Verarbeitung von verschachtelten Tags erweitert
    werden.
-   **\[Erledigt\]** Flexibilisierung des Loggers (evtl. Zusammenführen
    mit dem AdvancedLogger) (Diskussion siehe [Logger automatisch
    flushen](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=688)).
-   **\[Erledigt\]** Konfiguration des Log-Datei-Namens für
    Framework-Fehlermeldungen per Registry ermöglichen. Heute ist der
    Wert im Code hart auf "\*php.log" definiert.
-   **\[Erledigt\]** Erweiterung/Refactoring der Formular-Unterstützung:
    -   Refactoring Klassen-Namen basierend auf neuer Tag-Parser-Logik
        (eigener Punkt, da Formulare komplexer als einfache Tags sind)
    -   Implementierung eines *<form:label />*-Tags.
    -   Einführung einer Sichtbarkeit (*visibility* nicht wie bisher nur
        *read-only*) für alle Formular-Elemente mit der Möglichkeit bei
        Ausblenden eines Elements abhhängige Elemente ebenfalls
        automatisiert ausblenden zu können.
-   **Überarbeitung des GenericORMappers** (siehe [1.17
    GORM](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=1262))
    -   **\[Erledigt\]** Zusammenfassen von GenericORMapperUpdate und
        GenericORMapperSetup
    -   **\[Erledigt\]** Refactoring Konfiguration bei Benutzung des
        DISM mit gemischten Konfigurationen (statische und DI-injiziert)
        (siehe
        [6](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=1146&p=13208#p13208)).
-   **\[Erledigt\]** Erweiterung UMGT um alphanummerische Proxy-Ids
    (siehe
    [7](http://forum.adventure-php-framework.org/de/viewtopic.php?f=8&t=1306#p13120)).
-   **\[Erledigt\]** Erweiterung der Funktion des *PlaceHolderTag* um
    die Registrierung von String-Ersetzungen (Details siehe Diskussion
    unter
    [8](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=1162&start=45#p12977)).

Die fertig implementierten Features können
[hier](http://files.adventure-php-framework.org/snapshot/apf-1.17-snapshot-php5.tar.gz)
(stündliches Snapshot-Release) heruntergeladen werden.

Zur Migration auf die neue Version sind die unter [Migration von 1.16
auf 1.17](/Migration_von_1.16_auf_1.17 "wikilink") beschriebenen
Schritte notwendig.

## Version 1.16

In der Version 1.16 sollen nach aktuellem Stand folgende Features
entwickelt werden:

Wie unter [Release +
Dokumentation](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=689)
besprochen, beinhaltete das Release 1.15 bereits einen sehr großen
Umfang an Änderungen und Erweiterungen. Einige der folgenden Features
wurden daher von 1.15 nach 1.16 verschoben:

-   **\[Erledigt\]** Refactoring APF-Parser um TagLib-Naming-Konvention
    zu eliminieren und die Wiederverwendung von Klassen zu verbessern.
-   **\[Erledigt\]** Das *<form:date />*-Tag soll zukünftig das Attribut
    **tabindex** unterstützen um die Usability des Controls zu erhöhen.
    Weiterhin ist gewünscht, die Felder mit einer leeren Auswahl belegen
    zu können um den Benutzer zu einer aktiven Eingabe aufzuforden.
-   **\[Erledigt\]** Überarbeitung von ServiceManager und
    DIServiceManager: Context und Sprache sollen bei Singletons
    berücksichtigt werden. Zudem wird für den DIServiceManager ein neuer
    ServiceType "CACHED" eingeführt und der ServiceType "NORMAL"
    zukünftig nicht mehr gecached. (Diskussion im Forum:
    [9](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=1146))
-   **\[Erledigt\]** Hinzufügen der convenience-Methoden
    *transformChildren()* und *transformChildrenAsEmpty()* um die
    Transformation von Kinden zu vereinfachen. Weiterhin sollen die
    betroffenen Stellen auf die neuen Methoden umgestellt werden.
    Details siehe
    [10](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=1131&p=11802#p11802).
-   **\[Erledigt\]** Das Beziehungs-Management des GORM soll so
    erweitert werden, dass Beziehungen ebenfalls ein Erzeugungs~~- und
    Bearbeitungs~~datum erhalten.
-   **\[Erledigt\]** Erweiterung FilesystemManager wie unter
    [11](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=734)
    beschrieben.
-   **\[Erledigt\]** Erweiterung UMGT um Permanent-Login per Cookie
    ([12](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=692&p=8133#p8133)).
-   **\[Erledigt\]** Unterstützung HTML5-Attribute für Formular-Tags.
-   **\[Erledigt\]** Für die Unterstützung von HTML5-Attributen soll die
    Whitelist der Formular-Tags erweitert werden.
-   **\[Erledigt\]** Refactoring der API hinsichtlich der
    *setPlaceHolder\[IfExists\]()*-Methoden (siehe
    [13](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=743&start=30)).
-   **\[Erledigt\]** Implementierung der MultiFileUpload-Extension plus
    Entwerfen einer Dokumentation.
-   **\[Erledigt\]** Unterstützung einer alternativen
    Parser-Implementierung für Attribut-Delimiter oder Einführung von
    einfachen Platzhaltern (siehe
    [14](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=1162&p=12074#p12071)).
-   **\[Erledigt\]** Dokumentation des ExtendedSoapClientService, der in
    Release 1.15 hinzugefügt wurde (Ergebnis siehe
    [15](http://adventure-php-framework.org/Seite/148-ExtendedSoapClientService)).
-   **\[Erledigt\]** Implementierung eines Tags und eines Validators für
    ReCaptcha.
-   **\[Erledigt\]** Evaluierung *spl_object_hash($this)* statt
    *XmlParser::generateUniqID()* (nach ersten Tests ist
    spl_object_hash() um Faktor 4-5 schneller. **Ergebnis:**
    spl_object_hash() ist nicht kollisionsfrei. Daher wird weiterhin
    auf *XmlParser::generateUniqID()* gesetzt, jedoch wurde die interne
    Implementierung hinsichtlich Laufzeit optimiert.

Die fertig implementierten Features können aus dem
[SVN](http://adventurephpfra.svn.sourceforge.net/viewvc/adventurephpfra/branches/php5/1.16/)
bezogen werden.

Zur Migration auf die neue Version sind die unter [Migration von 1.15
auf 1.16](/Migration_von_1.15_auf_1.16 "wikilink") beschriebenen
Schritte notwendig.

## Version 1.15

Wie unter [Release +
Dokumentation](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=689)
besprochen, soll das Release 1.15 folgende Features enthalten:

-   **\[Erledigt\]** Erweiterung des Objekt-Typs "Rolle" um eine
    Beschreibung für die Rolle zur einfacheren Administration des
    Usermanagement-Moduls (siehe [Diskussion Usermanagement
    I](http://forum.adventure-php-framework.org/de/posting.php?mode=quote&f=5&p=4696#pr4693)).
-   **\[Erledigt\]** Erweiterung der Sichtbarkeits-Berechtigungen um die
    Qualität Lesen, Schreiben, Löschen, ... (siehe [Diskussion
    Usermanagement
    II](http://forum.adventure-php-framework.org/de/posting.php?mode=quote&f=5&p=4696#pr4701)).
-   **\[Erledigt\]** *template_taglib_addtaglib* in das APF aufnehmen
    (siehe
    [16](http://forum.adventure-php-framework.org/de/viewtopic.php?f=6&t=835&p=9304#p9304)).
-   **\[Erledigt\]** Unterstützung von Sockets für MySQL-Connections
    (siehe
    [17](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=340&p=9604#p9575)).
-   **\[Erledigt\]** Umstellung des UMGT auf Domänen-Objekte.
-   **\[Erledigt\]** Umstellung ServiceManager/DIServiceManager auf
    Interface APFService/APFDIService um Trennung zu APFObject zu
    erreichen.
-   **\[Erledigt\]** Einführung einer Setup-Methode für DI-Services
    inkl. Session-Wakeup.
-   **\[Erledigt\]** Umstellung UMGT auf DIServiceManager zur
    Konfiguration der Services.
-   **\[Erledigt\]** Erweiterung des UMGT-Daten-Modells zur einfacheren
    Pflege.
-   **\[Erledigt\]** Erweiterung Funktionalität Filter-Chain um Filter
    einfacher vor oder nach den bereits registrierten Filtern einhängen
    zu können (ebenso während der Laufzeit!)
-   **\[Erledigt\]** Erweiterung des GORM-Update-Tool, damit
    Beziehungs-Umbenennungen erkannt werden.
-   **\[Erledigt\]** Erweiterung des GORM-Domain-Objekt-Support um
    Type-Hints innerhalb der Code-Dokumentation und Erweiterung der
    Signatur der Base-Klassen um weitere Convenience-Methoden.
-   **\[Erledigt\]** Integration UMGT-Änderungen in die Sandbox.
-   **\[Erledigt\]** Erweiterung Singleton- und
    SessionSingleton-Implementierung um mehrfache, benannte Instanzen
    eines Services zu erlauben. Dies ist notwendig um per
    DIServiceManager mehrere benannte Instanzen einer identischen
    Implementierung (z.B. GORM) zu erzeugen und zu verwalten.
-   **\[Erledigt\]** Umstellung der UMGT-GUI auf konfigurierbare
    GUI-Beschriftung (Language-Files) für die Sprachen Deutsch und
    Englisch.
-   **\[Erledigt\]** Umbenennung des Input-Parameter für URL-Rewriting
    (siehe
    [18](http://forum.adventure-php-framework.org/de/viewtopic.php?f=7&t=889)).
-   **\[Erledigt\]** Refactoring der &lt;\*:getstring /&gt;-Tags in den
    core-Bereich, damit diese ohne &lt;\*:addtaglib /&gt; verwendet
    werden können.
-   **\[Erledigt\]** Datenbank-Treiber auf Basis von PDO, damit
    *prepared statements* einfacher unterstützt werden.

Die fertig implementierten Features können aus dem
[SVN](http://adventurephpfra.svn.sourceforge.net/viewvc/adventurephpfra/branches/php5/1.15/)
bezogen werden.

Zur Migration auf die neue Version sind die unter [Migration von 1.14
auf 1.15](/Migration_von_1.14_auf_1.15 "wikilink") beschriebenen
Schritte notwendig.

## Version 1.14

-   **\[Erledigt\]** Umstellung der Steuerung auf
    "Front-Controller-only". Damit wird es leichter, die Input- und
    Output-Filter-Chains zu koordinieren.
-   **\[Erledigt\]** Überarbeitung des *OnlyHTMLEntitiesFilter*, damit
    dieser nur dann HTML-Ersetzung vornimmt, wenn der Input-Filter dies
    nicht schon erledigt hat. Weiterhin soll der Filter den Zeichensatz
    des Formulars beachten, damit die Erstellung korrekt erfolgt.
-   **\[Erledigt\]** Refactoring des ServiceManager/DIServiceManager auf
    ein statisches Interface zur Performance-Steigerung.
-   Erweiterung HeaderManager um convenience-Methode um basierend auf
    der aktuellen URL direkt auf HTTPS weiterleiten zu können.
-   **\[Erledigt\]** Erweiterung des GCO (siehe [Verschachtelung für
    Criterion-Objekte?](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=417&p=3514&hilit=criterion#p3514)).
-   **\[Erledigt\]** Erweiterung des GenericCriterionObject um die
    Verwendung von größer/kleiner Vergleichen zu ermöglichen (siehe
    [GORM -&gt; Beziehungen mit Kriterien-Objekt
    laden](http://forum.adventure-php-framework.org/de/viewtopic.php?f=1&t=314)).
-   **\[Erledigt\]** Erweiterung des GORM zur Behandlung von
    Domänen-Objekten (siehe [GORM -
    Modellierungserweiterung](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=550)).
-   Erweiterung Sandbox um Login-Mechanismus mit automatischem Setup des
    UMGT.
-   **\[Erledigt\]** Refactoring / Erweiterung
    Link-Generierungs-Mechanismus um bestehende Anwendungen einfach mit
    einem neuen URL-Layout zu betreiben.
-   **\[Erledigt\]** Refactoring Front-Controller + Action Definition
    (optionale Input-Klasse, bessere API, Action-Typ als Konstanten,
    ...).
-   **\[Erledigt\]** Erweiterung Formular-Filter um die Möglichkeit
    einen regulären Ausdruck direkt für ein Feld angeben zu können.
-   **\[Erledigt\]** Einführen einer neuen Einstellung um das
    Character-Set zu definieren um Zeichensatz-Probleme zu vermeiden.
-   **\[Erledigt\]** Erweiterung der Passwort-Generierung im
    Usermanagement-Modul (Diskussion siehe [Umgt - Hardcoded
    Salt](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=581)).

Die fertigen Features können aus dem
[SVN](http://adventurephpfra.svn.sourceforge.net/viewvc/adventurephpfra/branches/php5/1.14/)
bezogen werden.

Zur Migration auf die neue Version sind die unter [Migration von 1.13
auf 1.14](/Migration_von_1.13_auf_1.14 "wikilink") beschriebenen
Schritte notwendig.

## Version 1.13

-   **\[Erledigt\]** Bugfixing & allgemeine Verbesserungen
-   **\[Erledigt\]** Refactoring des "alten" Gästebuch-Moduls und des
    Newspager auf aktuelle Technologien (Nutzung von jQuery statt
    nativer AJAX-Implementierung, Entfernen der
    *get()*-/*set()*-Methoden).
-   **\[Erledigt\]** Vereinheitlichung **SimpleSelectValidator** und
    **DefaultSelectValidator**.
-   **\[Erledigt\]** Erweiterung Konfiguration (Proposal siehe
    [Erweiterung Konfiguration](/Erweiterung_Konfiguration "wikilink"),
    Diskussion siehe [Erweiterung Konfiguration (Release
    1.12)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=117))
-   **\[Erledigt\]** Erweiterung Pager ([Erweiterung Pager (Release
    1.12)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=116),
    Dokumentation der Änderungen siehe [Migration von 1.12 auf
    1.13](/Migration_von_1.12_auf_1.13 "wikilink"))
-   **\[Erledigt\]** Umstellung aller mit dem APF ausgelieferten
    Komponenten auf Exceptions.
-   **\[Erledigt\]** Anpassung Front-Controller-Konfiguration: entfernen
    des Sub-Namespaces "actions", da dieser nicht wirklich benötigt
    wird. Dieser ist noch aus älteren Versionen aus Konventions-Gründen
    enthalten (Migration siehe [Migration von 1.12 auf
    1.13\#Frontcontroller](/Migration_von_1.12_auf_1.13#Frontcontroller "wikilink")).
-   **\[Erledigt\]** *bulk setting* für Platzhalter von Formularen
    (siehe [setPlaceholder - multiple
    version?](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=364&start=0)).
-   **\[Erledigt\]** Fluent Interface für das GenericCriterionObject
    (siehe [Fluent Interface des
    GenericCriterionObject](/Fluent_Interface_des_GenericCriterionObject "wikilink")).
-   **\[Erledigt\]** Einbau einer Möglichkeit um den
    Attribut-Whitelist-Filter für Formulare erweitern zu können (siehe
    [Whitelist von Form control
    erweitern](http://forum.adventure-php-framework.org/de/viewtopic.php?f=6&t=366)).
-   **\[Erledigt\]** Erweiterung des CacheManager um eine CMS-Seite mit
    unterschiedlichen Modulen cachen zu können (siehe [Erweiterung
    CacheManager](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=378);
    Quellcode findet sich im SVN ab Revision 1000).
-   **\[Erledigt\]** Erweiterung des PagerManager um Methoden zur
    Abfrage von Seiten-Informationen (siehe [Pager -&gt; Weiterleitung
    auf die richtige
    Seite](http://forum.adventure-php-framework.org/de/viewtopic.php?f=1&t=392&p=3454#p3445),
    Dokumentation der Änderungen siehe [Migration von 1.12 auf
    1.13](/Migration_von_1.12_auf_1.13 "wikilink")).
-   **\[Erledigt\]** Erweiterung des aktuellen Input- und
    Output-Filter-Mechanismus, um mehrere Filter registrieren zu können
    (Filter-Chain für Input- **und** Output-Filter). Details können der
    Diskussion um die [Erweiterung der
    htmlheader-Extension](http://forum.adventure-php-framework.org/de/viewtopic.php?f=11&t=207&p=4967#p4965)
    entnommen werden.

Die fertigen Features können aus dem
[SVN](http://adventurephpfra.svn.sourceforge.net/viewvc/adventurephpfra/branches/php5/1.13/)
bezogen werden.

## Version 1.12

-   **\[Erledigt\]** Bugfixing & allgemeine Verbesserungen
-   **\[Erledigt\]** Umstellung Page-Controller auf Exceptions.
-   **\[Erledigt\]** Refactoring der APF-API: dedizierte Methoden statt
    generische *get()*- und *set()*-Methoden! Hinweise zu den Änderungen
    finden sich auf der Seite [API-Änderungen in Version
    1.12](/API-Änderungen_in_Version_1.12 "wikilink").
-   Umbenennung diverser Klassen um dem JAVA-Coding-Standard zu genügen
    (siehe auch [API-Änderungen in Version
    1.12](/API-Änderungen_in_Version_1.12 "wikilink")):
    -   **\[Erledigt\]** connectionManager -&gt; ConnectionManager
    -   **\[Erledigt\]** configurationManager -&gt; ConfigurationManager
    -   **\[Erledigt\]** coreObject -&gt; APFObject
-   **\[Erledigt\]** Beerdigung des PHP4-Zweiges
-   **\[Erledigt\]** Zusätzliche Indizes für Setup-/Update-Tool
    (Proposal siehe [Zusätzliche Indizes für Setup-/Update-Tool
    GORM](/Zusätzliche_Indizes_für_Setup-/Update-Tool_GORM "wikilink"))
-   **\[Erledigt\]** Erweiterung <core:appendnode />-Tag (Proposal siehe
    [Erweiterung <core:appendnode />-Tag (Release
    1.12)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=243))
-   **\[Erledigt\]** Umstellung GORM auf Exceptions statt
    *trigger_error()*
-   **\[Erledigt\]** Einführung eines "Timecaptcha"-Feldes zum Schutz
    von Formularen.
-   **\[Erledigt\]** Die Formular-Validatoren sollen zukünftig die
    Fehler-Markierung nicht mehr per Inline-Style sondern per
    CSS-Klassen erzeugen. Hierzu soll eine definierte Klasse an das
    Formular-Element angehängt werden, das vom Entwickler gestyled
    werden kann (Proposal siehe [Weiterentwicklung
    Formular-Validierung](/Weiterentwicklung_Formular-Validierung "wikilink")).
-   **\[Erledigt\]** Erweiterung Formular-Validatoren: es soll möglich
    sein, spezielle Listener je nach Validator anzusteuern (Proposal
    siehe [Spezielle
    Validator-Listener](/Spezielle_Validator-Listener "wikilink")).
-   **\[Erledigt\]** Das GORM-Setup- und -Update-Tool soll die
    Möglichkeit erhalten, den Datentyp für die nummerischen Spalten zu
    definieren. Bisher ist nur "INT" möglich, durch eine neue Methode
    sollen auch andere Daten-Formate möglich sein (ist mit Revision 872
    im SVN verfügbar).
-   **\[Erledigt\]** Aufräum-Arbeiten des Debug-Modus der DB-Treiber
    (Proposal siehe [datenbanktreiber - debugmode und
    exceptions](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=308#p2670)).
-   **\[Erledigt\]** Erweiterung umgt-Modul um Rechte-Management über
    Proxy-Objekte. Siehe Diskussion unter [Benutzerverwaltung
    integrieren](http://forum.adventure-php-framework.org/de/viewtopic.php?f=4&t=301&p=2609#p2609),
    bzw. Proposal unter [Sichtbarkeits-Berechtigungen im
    umgt-Modul](/Sichtbarkeits-Berechtigungen_im_umgt-Modul "wikilink").

## Version 1.11

-   Refactoring der Formular-Taglibs (Proposal siehe [Refactoring
    Form-Taglibs (Release
    1.11)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=168))
-   Erweiterung Iterator-Tag (Proposal siehe [item:addtaglib &
    item:getstring (Release
    1.11)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=225))
-   Erweiterung umgtManager (Proposal siehe [Erweiterung umgtManager
    (Release
    1.11)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=171))
-   Erweiterung GenericORMapper (Proposal siehe [Erweiterung GORM
    (Release
    1.11)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=154))
-   Erweiterung GORM-Setup/-Update (Proposal siehe [Erweiterung
    GORM-Setup (Release
    1.11)](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=131))
-   Bugfixing & allgemeine Verbesserungen

**Die folgende Auflistung zeigt die Inhalte der älteren Releases:**

## Version 1.10

-   Erweiterung der PHP 5 Kompatibilität (Einführung der üblichen
    Sprachkonstrukte)
-   Erweiterung der Funktion des GenericORMappers (Gleichzeitiges
    Löschen von Elementen während des Speicherns von Bäumen)
-   Neue Komponente DIServiceManager als expliziten dependency injection
    Container. Bisher fungierte der ServiceManager bereits als
    indirektes Mittel für dependency injection.
-   Redesign der Formular-Validierung inkl. Einführung der
    Mehrfachvalidierung.
-   Bugfixing & allgemeine Verbesserungen

## Version 1.9

-   Implementierung eines CookieManager analog zum SessionManager
-   Ermöglichen einer mehrfachen Validierung von Formularfeldern.
-   Request- und Response-Filter über die Registry konfigurierbar
    machen.
-   Filter für Form-Felder einführen.
-   Überarbeitung des Usermanagement und Dokumentation der Komponente.
-   Bugfixing & allgemeine Verbesserungen

## Version 1.8

-   Optimierung und Weiterentwicklung des GenericORMapper und des darauf
    aufsetzenenden usermanagement-Moduls.
-   Neuentwicklung des bbCodeParsers nach Anforderungen aus dem Forum.
-   Implementierung einer AdvancedLogger-Komponente, die durch die
    APF-Mechanismen konfiguriert werden kann.
-   Tutorial für die RSS-Ausgabe von bestehenden Seiten.
-   Neuentwicklung des CacheManagers.
-   Redesign des filesystemHandler.
-   Implementierung eines HeaderManager als Wrapper für die PHP-Funktion
    header() gemäß Wunsch im Forum.
-   Einführung eines Media-Streamers. Dieser kann per Tag eingebunden
    werden und beliebige Medien aus definierten Namespaces ausgeben. Das
    Modul bringt dabei nicht nur den Tag zur Einbindung in Templates und
    Formulare mit, sondern beinhaltet auch die relevante FrontController
    Action.
-   Verfassen des Artikels "behind the site" für die neue
    Dokumentationsseite.
-   Bugfixing & allgemeine Verbesserungen

## Version 1.7

-   Redesign der Dokumentationsseite inkl. Suchmaschinen-Optimierung
-   Einführen der Registry und Ersetzen des ApplicationManagers für
    konfigurationsloses einsetzen des Frameworks.
-   Einführung einer OR-Mapper-Komponente, die nicht nur Objekte in
    einer relationalen Datenbank abbilden kann (Table-Data-Gateway,
    Row-Data-Gateway), sondern komplette Objektbäume laden und
    Persistieren kann (GenericORMapper).
-   Auslieferung eines Usermanagement-Moduls, das generisch für
    verschiedene Applikationen eingesetzt werden kann. Das Modul basiert
    auf dem generischen OR-Mapper.
-   Weitere Tutorials (Behind the site) und Übersetzung der restlichen
    Dokumentation.
-   Auslieferung von getrennten Releases für PHP4 und PHP5.
-   Bugfixing & allgemeine Verbesserungen

## Version 1.6

-   Übersetzung der restlichen Tutorials
-   Erweiterung der Tutorials und Einsteiger-Materialien
    (FrontController!)
-   Tutorial für Frontcontroller-basiertes Login-Formular bzw.
    Bild-Ausgabe via Action
-   Singleton-Klasse um SessionSingleton-Klasse erweitern
-   Einfühung connectionManager zur Abstraktion von DB-Treibern
-   Einführung eines SQLite DB-Treibers
-   Bugfixing & allgemeine Verbesserungen
-   CAPTCHA-Formular-Control
-   Spam-Schutz für Gästebuch inkl. Tutorial und Dokumentation