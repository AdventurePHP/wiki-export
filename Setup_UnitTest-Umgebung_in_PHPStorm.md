Dieses HOWTO beschreibt die Konfiguration von PHPStorm um APF-UnitTests
ausführen zu können.

## Einstieg

JetBrains stellt im Rahmen einer Open Source Software Lizenz den
Entwicklern des APF eine kostenlose Lizenz zur Verfügung. Diese kann per
PN an Christian beantragt werden. PHPStorm in der jeweils aktuellen
Fassung kann unter <http://www.jetbrains.com/phpstorm/> heruntergeladen
werden.

## Vorbereitung

Um die Konfiguration vornehmen zu können, sind folgende Vorbereitungen
notwendig:

### Auschecken APF-Quellen

Die jeweils aktuellen APF-Quellen können aus dem GitHub-Repository unter
<https://github.com/AdventurePHP/code> ausgecheckt werden. Hier ist
insbesondere der Ordner *test* relevant, da dieser die UnitTest-Umgebung
des APF beinhaltet.

### Setup der PHPUnit-Umgebung

Zur Installation von PHPUnit wird \[composer <https://getcomposer.org>\]
benötigt. Dieser muss in PHPStorm wie folgt konfiguriert werden:

[Datei:APF-PHPUnit-composer.png](/Datei:APF-PHPUnit-composer.png "wikilink")

Anschließend können Sie ein Command Line Alias erstellen um die
Installation der Abhängigkeiten durch Composer vorzunehmen:

[Datei:APF-PHPUnit-shell.png](/Datei:APF-PHPUnit-shell.png "wikilink")

## Installation

Dis Installation von PHPUnit kann nun mit Hilfe der Command Line (STRG +
SHIFT + X) ausgeführt werden. Geben Sie hierzu

``` bash
c install
```

ein:

[Datei:APF-PHPUnit-install.png](/Datei:APF-PHPUnit-install.png "wikilink")

## Konfiguration

Zur Ausführung von PHPUnit in PHPStorm muss nun eine Konfiguration
angelegt werden. Die Einstellungen finden sich im oberen Bereich der
Toolbar:

[Datei:Phpstorm_unittests_05.png](/Datei:Phpstorm_unittests_05.png "wikilink")

Nach dem Klick auf *Edit Configurations* öffnet sich der Dialog, in dem
u.a. auch die Debugger-Einstellungen zu finden sind. Dort wenden wir uns
nun den Konfigurationen im Bereich *PHPUnit* zu. Erstellen Sie dort nun
mut Hilfe des Plus-Symbols und der anschließenden Auswahl von *PHPUnit*
eine neue Konfiguration.

[Datei:Phpstorm_unittests_07.png](/Datei:Phpstorm_unittests_07.png "wikilink")

Dort vergeben wir nun folgende Werte:

[Datei:APF-PHPUnit-config.png](/Datei:APF-PHPUnit-config.png "wikilink")

Die Werte haben folgende Bedeutung:

-   **Test scope**: hiermit legen wir fest, dass die ausgeführten Tests
    in einer eigenen Konfiguration definiert sind. Die
    *phpunit.dist.xml* beinhaltet hierzu zwei Suites für *core* und
    *tools*. Gleichzeitig referenziert die zweite Eingabemöglichkeit
    eine eigene Bootstrap-Datei, die eine APF-Umgebung für die UnitTests
    schafft.
-   **Interpreter options**: Damit die PHPUnit-Libraries auch beim
    Ausführen der Tests auf der Komandozeile verfügbar sind, müssen die
    notwendigen Pfade in den include_path hinzugefügt werden.
-   **Custom working directory**: hiermit wird PHPUnit mitgeteilt, in
    welchem Basis-Verzeichnis die Tests ausgeführt werden sollen.

Alle referenzierten Ordner und Dateien finden Sie in den ausgecheckten
PHP-Quellen.

Nachdem der Dialog bestätigt wurde, sehen Sie die neue Konfiguration im
oberen Bereich von PHPStorm:

[Datei:Phpstorm_unittests_09.png](/Datei:Phpstorm_unittests_09.png "wikilink")

## Ausführen der Tests

Mit Klick auf den grünen Pfeil direkt rechts neben der Auswahl können
alle APF-Tests ausgeführt werden. Die Ausgabe sieht ungefähr wie folgt
aus:

[Datei:APF-PHPUnit-run.png](/Datei:APF-PHPUnit-run.png "wikilink")