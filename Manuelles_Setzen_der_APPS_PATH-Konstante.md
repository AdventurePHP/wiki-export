<translate> **Bitte beachten Sie, dass diese Dokumentation
ausschließlich für die Produkt-Linie 1.X gilt. Details zur Konfiguration
des ClassLoader in Version 2.X finden Sie unter
[1](http://adventure-php-framework.org/Seite/154-Laden-von-Klassen#Chapter-4-Anwendung).**

------------------------------------------------------------------------

Sofern eine APF-Installation für mehrere Projekte auf einem Server
genutzt wird und dieses per Symlinks eingeklinkt wird, kann es notwendig
sein, den LibPath manuell zu setzen. Hierzu nutz das APF intern die
Konstante **APPS__PATH** um teure Zugriffe auf die Registry zu
vermeiden.

Wie unter [intelligentes APPS_PATH
setzen](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=421&p=3658)
diskutiert wurde, gibt es mit dem Release 1.13 (bzw. dem SVN-Commit
1033) die Möglichkeit, diese selbst vor dem Einbinden der Datei
**pagecontroller.php** zu setzen:

``` php
define('APPS__PATH','/my/custom/path');
include('./apps/core/pagecontroller/pagecontroller.php');
...
```

Der Inhalt der Konstanten muss wie auch in der dynamischen Variante auf
dasjenige Verzeichnis zeigen, das die Ordner

-   core
-   module
-   tools
-   ...

enthält. </translate> <languages />