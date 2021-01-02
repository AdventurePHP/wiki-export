## Einleitung

Angeregt durch die Diskussion im Foren-Thread [URL-Rewriting wie in
Rails](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=269)
soll dieses HOWTO zeigen, wie das APF erweitert werden kann um diese Art
des URL-Handlings zu unterstützen.

Hierbei gibt es mehrere Möglichkeiten der Umsetzung, denn das
[Rails-Framework](http://rubyonrails.org) basiert weitestgehend auf dem
Prinzip *convention over configuration*. Dies wird in einigen Teilen
durch explizite Konfigurationen hinsichtlich des URL-Mappings erweitert,
das Paradigma findet jedoch weitestgehend Anwendung.

## Vorüberlegungen

Um die Flexibilität des APF hinsichtlich der URL-Layouts nicht gänzlich
aufgeben zu müssen, soll das URL-Mapping nur auf einen Teil des
DOM-Baums Einfluss haben.

Weiterhin soll es Ziel der hier beschriebenen Umsetzung sein, möglichst
viele Teile des APF weiter zu verwenden. Da das URL-Layout maßgeblich
von einem
[Input-Filter](http://adventure-php-framework.org/Seite/087-Filter)
bestimmt ist, soll auch in diesem Fall ein Filter implementiert werden,
der die Konvention versteht und die notwendigen Schritte einleitet.
Hierunter Fallen die Analyse der URL hinsichtlich auszuführendem
Controller, auszuführender Action und der Befüllung des
Applikations-Models mit den in der URL befindlichen Parametern.

Da im Falle des URL Layouts des Rails-Framework ein (Front-)Controller
eingesetzt wird, soll dies auch bei der APF-Umsetzung so gewählt werden.
Hierzu verwenden wir den
[APF-Front-Controller](http://adventure-php-framework.org/Seite/012-Frontcontroller).
Dieser stellt eine zentralen Einstieg in die Applikation dar. Alle in
der URL abgebildeten Actions sollen dann per Front-Controller-Action
abgebildet werden.

## Umsetzung des Filters

Der Input-Filer übernimmt - wie Eingangs erwähnt - das Übersetzen des
URL-Layouts in Modul, Action und Parameter. Hierzu wird folgendes
(statische) Layout vorgeschlagen:

``` bash
/<Modul>/<Action>/<Param1>/<Param2>/...
```

Um diese Art der URL zu ermöglichen ist für jede Applikation ein eigener
VHOST notwendig. Alternativ dazu kann in der Applikation ein Sub-Pfad
definiert werden, der beim Mapping nicht berücksichtigt wird. Damit ist
der Betrieb von mehreren Applikationen unter einer Domain möglich.
Letzeres soll erst im zweiten Schritt betrachtet werden.

### Aufbau des Filters

Ein Input-Filter im Sinne des APF ist durch die APF-Filter-API
definiert. Es besitzt folgende Basis-Struktur:

``` php
class RailsUrlLayoutRequestFilter extends AbstractFilter {

   public function filter($input) {
   }

}
```

Innerhalb der Filter-Klasse wird dann die notwendige Logik platziert.
Damit dieser Filter auch wirklich ausgeführt wird, muss er zunächst in
der Registry als Input-Filter für den Front-Controller registriert
werden. Dies passiert in der *index.php* wie folgt:

``` php
require('../apps/core/bootstrap.php');

use VENDOR\rails\filter\input\RailsUrlLayoutRequestFilter;

InputFilterChain::getInstance()->appendFilter(new RailsUrlLayoutRequestFilter());
```

Um den Filter in der oben beschriebenen Art und Weise einsetzen zu
können, muss noch ein VHOST mit einer RewriteRule angelegt werden, die
jegliche Anfragen auf die beschriebene *index.php* leitet. Das Anlegen
eines VHOSTS ist in der [Apache
Dokumentation](http://httpd.apache.org/docs/2.2/vhosts/) zu finden, die
RewriteRule soll in einer *.htaccess*-Datei innerhalb des DocumentRoot
platziert werden und folgende Aussage haben:

``` apache
RewriteEngine on
RewriteRule !(index\.php|\.css|\.jpe?g|\.png|\.gif|\.zip|\.ico|\.txt)$ /index.php [NC,L]
```

### Implementierung des Filters

Die Aufgabe des Filters ist es nun, den Request zu analysieren und gemäß
der folgenden Konvention eine Entscheidung hinsichtlich der Ausführung
von Actions und dem Aufbau der GUI zu treffen:

-   Der erste Bereich der URL stellt den Namen des Moduls dar. Dieser
    wird als Sub-Namespace aufgefasst, was bedeutet, dass alle
    Rails-style Applikationen unter *VENDOR\\rails\\apps\\<Modul>*
    abgelegt werden.
-   Der Namen des Moduls ist gleichzeitig Teil des Namens des
    Applikations-(View-)Models. Dieser folgt der Konvention "*Name des
    Moduls mit ersten Buchstaben groß*" + "*Model*". Beispiel bei Aufruf
    von **/pages** -&gt; **PagesModel**.
-   Der zweite Bereich der URL stellt den Namen der Action dar, die
    ausgeführt werden soll. Diese wird per Konvention unter dem
    Namespace *VENDOR\\rails\\apps\\<Modul>\\pres\\actions* gesucht und
    erhält den Namen "*<Action>*" + "*Action*". Bei Aufruf von
    **/pages/display** -&gt; **DisplayAction**.
-   Alle weiteren Bereiche werden 1:1 auf die Parameter des Models
    gemappt. Dabei ist die Reihenfolge der Methoden entscheidend. Soll
    der erste Parameter die ID der Seite sein, soll muss die erste
    Methode (ausser dem Konstruktur des Models) auch die Methode
    *setId()* sein.

... to be continued ...