Diese Seite bietet einen Überblick über die bereits vorgeschlagene
Features für die Version 2.0 des APFs. Alle gelisteten Punkte sind
**reine Vorschläge** und wurden noch nicht oder nur in geringem Maß
diskutiert - es steht daher noch offen, ob und wie die einzelne Punkte
tatsächlich implementiert werden. Einige Features sind auch von der bis
dahin etablierten PHP Version abhängig.

In der [2.xx
Ideensammlung](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=340)
ist es möglich, neue Vorschläge einzubringen oder über Bestehende zu
diskutieren.

## Allgemein

-   **PHP 5 &gt;= 5.3.0** Die seit PHP 5.3 verfügbaren Namensräume
    sollen die jetzigen ablösen.
    -   Eine entsprechende __autoload() Funktion könnte dann die
        import() Funktion ersetzen.
-   Es soll die Möglichkeit existieren, jeden Komponenten im Framework
    austauschen zu können. Dafür sollen die Klassen in der Registry
    abgelegt werden, sodass sie problemlos durch neue ersetzt werden
    können.
-   Es sollen nicht mehr alle Module und vor allem Erweiterungen
    standardgemäß mitgeliefert werden. Über eine GUI soll die
    Möglichkeit gegeben werden, die benötigten Komponenten auszuwählen
    um so ein individuelles Paket zu erhalten.
    -   Da jedes Modul und jede Erweiterung in einem eigenem Ordner
        liegt, könnte ein solcher *Package Builder* über das
        SVN-Repository realisiert werden.
    -   Es muss trotzdem noch möglich sein, das komplette Framework zu
        beziehen.
-   LIBs sollen auf Basis entsprechender Wrapper oder einem
    *ClassLoader* eingebunden und verwendet werden können.
-   Das APF soll über einen speziellen CLI Frontcontroller verfügen um
    für diese Anwendungen einfacher verwendbar zu werden.

## Datenbanken und der GORM

-   Der GORM soll von *modules/* nach *tools/* verschoben werden.
-   Der GORM soll für alle Datenbanksysteme verwendet werden können. Um
    dies zu erreichen, müssen je nach System andere Querys generiert
    werden. Dies könnte mittels ...
    -   ... statischen Dateien, die die vorgefertigen Querys enthalten
        oder mittels ...
    -   ... injizierbaren Providern realisiert werden.
-   Durch Refaktorierung der Datenbanktreiber soll erreicht werden, dass
    jeder Treiber die gleiche Funktionalität bietet. So soll zum
    Beispiel auch der MySQL Treiber *Prepared Statements* unterstützen
    beziehungsweise simulieren.
-   **PHP 5 &gt;= 5.1.0** Refaktorierung der Datenbanktreiber zur
    Einbeziehung von PDO
-   Weitere Datenbanktreiber: Oracle, PostgreSQL und MSSQL.

## Coding-Stil

-   Umstellung aller Konstruktoren auf die __construct() Notation.
-   Zum Einrücken sollen vier Leerzeichen oder Tabs verwendet werden.

## Formulare

-   Die form:addvalidator Taglib soll auch den entsprechenden
    Clientvalidator hinzufügen.
    -   Dazu muss die *clientform* Erweiterung in das APF integriert
        werden.
-   Eine form:prefill Taglib zum einfachen Vorbefüllen von
    Formularfeldern.
-   Formulare sollen auf Basis von *WorkFlows* über
    Konfigurationsdateien generiert werden.