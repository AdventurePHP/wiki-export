## Einleitung

Es handelt sich bei dieser Extension um eine Abwandlung des im APF
enthaltenen Pager-Moduls. Grund hierfür war vor Allem die strikte
Bindung des eigentlichen Pagers an die Verwendung von Statement-Files.
In manchen Fällen ist es jedoch nicht ohne Weiteres möglich eben solche
Dateien zu verwenden, da die Dynamik sich auf das Füllen von
Feldinhalten beschränkt. Entsprechend war meine Idee einen Pager zu
konstruieren, bei dem die Quelle der Daten beliebig gewählt werden kann.
Dies hat natürlich zum "Nachteil" (im Vergleich zum APF-Pager), dass man
sich als Programmierer um die Quelle der Daten selber kümmern muss. Der
ArrayPager ist dafür gedacht, Daten-Arrays in der Session zu speichern
und von beliebiger Stelle im Code aus einen Pager zu generieren und die
zur aktuell aufgerufenen Seite gehörenden Daten auszulesen. Ein
wichtiger Vorteil ist jedoch, dass bereits von Anfang an eine
Unabhängigkeit im Bezug auf Sprachen besteht, da sprachliche Inhalte
komplett in eine Sprachdatei ausgelagert wurden. Über die üblichen
Funktionen mittels <html:getstring /> werden diese Inhalte im Template
eingebunden. Es können verschiedene Konfigurationen definiert werden,
die jeweils einem Manager bei der Erstellung über die Fabric zugewiesen
werden. Das Aussehen des Pagers hängt also vom verwendeten Manager ab.
Dabei spielt es keine Rolle über welchen Manager das Daten-Array
registriert wurde. Die Formatierung des Pagers entsteht erst bei Abruf
der entsprechenden Methoden und dann im Bezug zum verwendeten Manager.

## Konfiguration

Zur Verwendung des ArrayPagers sind zunächst 2 Konfigurationsdateien von
Nöten. Diese müssen im Ordner /config/extensions/arraypager/{CONTEXT}/
platziert werden. Die beiden Dateien im Überblick:

{ENVIRONMENT}_language.ini

``` ini
[de]
DisplayNEntriesPerPage = "Einträge pro Seite anzeigen"
DisplayNextPage = "Nächste Seite anzeigen"
DisplayPreviousPage = "Vorherige Seite anzeigen"
DisplayPage = "Zeige Seite"
Entries = "Einträge/Seite"
```

Die Parameter geben die Sprachvariablen an, die beliebig angepasst und
für verschiedene Sprachen erweitert werden können. Es wird jedoch nur
eine Sprach-Datei unterstützt.

{ENVIRONMENT}_arraypager.ini

``` ini
[PagerSection]
DesignNamespace = "APF\extensions\arraypager\pres\templates"
DesignTemplate = "arraypager"
[Entries = "10"]
[EntriesChangeable = "false|true"]
[ParameterPage = "page"]
[ParameterEntries = "entries"]
[EntriesPossible = "5|10|15"]
```

Die Konfiguration der einzelnen Pager sieht lediglich 2 Pflichtfelder
vor: Namespace und Name des Templates (Pager.DesignNamespace und
Pager.DesignTemplate). Für die Erstellung eigener Templates sollten die
Original-Dateien zu Rate gezogen und manipuliert werden. Alle weiteren
Parameter sind optional:

-   **Entries**: Gibt die Anzahl der Beiträge je Seite an. Standard: 10
-   **EntriesChangeable**: De-/Aktiviert die Möglichkeit die Anzahl der
    Beiträge je Seite zu manipulieren. Standard: "false"
-   **ParameterPage**: Definiert den URL-Parameter zur Manipulation der
    aktuellen Seite. Standard: "page"
-   **ParameterEntries**: Definiert den URL-Parameter zur Manipulation
    der Beiträge je Seite. Standard: "entries"
-   **EntriesPossible**: Eine Liste von durch Pipe "\|" getrennten
    Angaben, die bei EntriesChangeable="true" ausgewählt werden können.
    Standard: "5\|10\|15"

Zu beachten ist hierbei folgendes: Jede Pager-Sektion dient einzig der
Konfiguration der Pager-Möglichkeiten und dem Aussehen (durch die
Zuweisung eines Templates). Die Daten bezieht der Pager aus dem Mapper
dem zur Laufzeit eine Daten-Liste übergeben werden muss.

## Verwendung des ArrayPagers

Zur Verwendung des ArrayPagers wird eine Fabric genutzt, über die eine
Manager-Instanz generiert wird:

``` php
use APF\extensions\arraypager\biz\ArrayPagerManagerFabric;

// [...] Ihr Script-Code

// ArrayPagerManagerFabric instanziieren
$oAPMF = $this->getServiceObject('APF\extensions\arraypager\biz\ArrayPagerManagerFabric');

// ArrayPagerManager über Fabric laden
$oAPM = $oAPMF->getArrayPagerManager ('PagerSection');

// [...] weiterer Code
```

Der Instanz des ArrayPagerManagers wird mit der Übergabe des Parameters
an die Fabric die zu verwendende Konfigurations-Sektion zugewiesen. Es
können mehrere Instanzen des Managers existieren, die unterschiedliche
Konfigurationen nutzen. Alle registrierten Pager können mit allen
Instanzen verwendet werden. Das Aussehen wird dann entsprechend der
Konfiguration des verwendeten Managers angepasst.

Zur Verwendung des Pagers muss ein Daten-Array über den Manager
registriert werden:

``` php
// Registrieren eines Daten-Arrays zur Verwendung des Pagers
$oAPM->registerPager ('NameDesPagers',
                      $DataArray
                      );
```

Hierbei wird das komplette Daten-Array in der Session gespeichert. Es
besteht also keine Notwendigkeit bei jedem Seitenaufruf das Daten-Array
erneut zu generieren und zu speichern, wenngleich dieses trotzdem
möglich ist. Verwendet werden kann jede Form eines Arrays, ob ein- oder
mehrdimensional. Es kann sich auch um ein eindimensionales Array mit
Klassen-Instanzen handeln, wobei in diesem Fall der nachfolgende Hinweis
zu beachten ist.

**Hinweis:** Die Speicherung von reinen Daten-Arrays funktioniert
problemlos. Wird jedoch ein Array mit Klassen-Instanzen gespeichert
müssen vor dem Laden des SessionManagers (z.B. durch das Laden der
ArrayPagerManagerFabric (diese bindet ArrayPagerManager und diese
wiederum ArrayPagerMapper ein. Letztere benötigt den SessionManager,
weshalb dieser ebenfalls geladen wird) oder durch die Verwendung der
Methoden getServiceObject in Verbindung mit SESSIONSINGLETON-Objekten)
alle verwendeten Klassen eingebunden sein. Ist dies nicht der Fall wirft
PHP beim Laden der Session-Daten einen Fehler, dass die Klassen nicht
ordnungsgemäß geladen werden konnten.

Nachdem die Daten in der Session registriert sind können sie in jedem
Script mit Zugriff auf die Session den Pager laden und verwenden.
Hierfür stehen zwei Methoden zur Verfügung:

``` php
//Liefert die aktuelle Seite mit der angegebenen Anzahl Beiträgen je Seite zurück
$DataList = $oAPM->loadEntries ('NameDesPagers');

// Lädt den Pager als fertige HTML-Ausgabe
$HTMLOutput = $oAPM->getPager ('NameDesPagers');
```

## Prüfungs-Methoden

Zur Überprüfung, ob ein Pager bereits registriert ist kann folgender
Code verwendet werden:

``` php
if ($oAMP->checkPager ('NameDesPagers') === FALSE)
{
  // Pager existiert nicht
}
```

Dieser Code zeigt die Prüfung, ob Seiten-Parameter in der URL enthalten
sind, die der Konfiguration des Managers entsprechen:

``` php
if ($oAMP->checkPage () === FALSE)
{
  // Seiten-Parameter nicht vorhanden
}
```

Mit diesen beiden Methoden lässt sich bereits ein einfaches Caching
realisieren. Hierbei wird bei a) jedem Seitenaufruf ohne
Seiten-Parameter (also z.B. bei einem Aufruf aus einem Menü-Link) und b)
noch nicht registriertem Pager das Laden der im Pager zu verwendenden
Daten und das Registrieren dieser über den Manager realisiert:

``` php
if ($oAMP->checkPager ('NameDesPagers') === FALSE
OR  $oAMP->checkPage ()                 === FALSE
    )
{
  // Pager existiert nicht oder das Script
  // wurde ohne Seiten-Parameter aufgerufen
  $DataArray = LadenDesDatenArrays ();

  $oAMP->registerPager ('NameDesPagers',
                        $DataArray
                        );
}
```

Dem Entwickler bleibt es überlassen, ob er die Struktur bei jedem
Seitenaufruf erneut registriert oder ob er nur zu bestimmten Kriterien
die Daten neu lädt.

## Anpassen der Templates

Grundsätzlich steht es dem Entwickler frei, wie ein Template auszusehen
hat. Man sollte sich jedoch an dem vorhandenen Document-Controller
"ArrayPager" orientieren. Die aktuelle Seite wird hierbei im
Document-Controller ermittelt und als Grundlage dafür genommen, welcher
Seiten-Link als aktiv zu markieren ist. Über den Manager wird das
Attribut "DataCount" an den Document-Controller übergeben, ebenso wie
das Attribut "Config", wobei es sich im ersten Fall um einen
Integer-Wert und im Zweiten um ein Array handelt. Man kann innerhalb des
Document-Controllers ganz normal über das Attributes-Array oder die
Methode getAttribute() an die Daten herankommen.

Das Config-Array hat dabei folgende Inhalte:

``` php
$this->attributes = array ('Entries'           => 10,        // [Integer]: Anzahl Einträge je Seite
                       'EntriesPossible'   => '5|10|15', // [String]:  Mögliche Einträge je Seite
                       'EntriesChangeable' => TRUE,      // [Boolean]: De-/Aktiviert die Manipulationsmöglichkeit von Entries
                       'ParameterPage'     => 'page',    // [String]:  URL-Parameter; Aktuelle Seite
                       'ParameterEntries'  => 'entries'  // [String]:  URL-Parameter; Einträge je Seite
                       );
```

Wichtig in dieser Hinsicht ist eigentlich nur, dass man darauf achtet,
dass bei EntriesChangeable=FALSE keine Elemente zur Manipulation der
Einträge je Seite erfolgt.

## Beispiel-Konfiguration

Ein Pager mit standardmäßig 6 Beiträgen je Seite und der Möglichkeit
zwischen 3, 6 und 9 Beiträgen je Seite wechseln zu können sollte so
aussehen:

``` ini
[PagerSection]
DesignNamespace = "APF\extensions\arraypager\pres\templates"
DesignTemplate = "pager"
Entries = "6"
EntriesChangeable = "true"
EntriesPossible = "3|6|9"
```