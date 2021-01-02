## Einleitung

Der Page-Controller des APF ist die Kern-Komponente des Frameworks. Sie
kümmerst sich um den Aufbau des Dokumenten-Baums (HMVC-DOM-Baum), die
Verwaltung und die Generierung der HTML-Repräsentation desselben. Dabei
wird der Baum aus Instanzen von
[Taglib](http://adventure-php-framework.org/Seite/046-Standard-TagLibs)-Klassen
aufgebaut und mit Hilfe von Document-Controllern und der Logik der
[Taglibs](http://adventure-php-framework.org/Seite/045-TagLibs-erstellen)
zu einem HTML-Quelltext transformiert.

Es finden also - wenn man so möchte - zwei Transformationen statt:

-   Die Assemblierung eines Dokumenten-Baumes aus einzelnen Templates,
    Taglibs und Controllern.
-   Die rekursive Transformation des Baumes in seine
    HTML-Text-Entsprechung.

Jede der genannten Komponenten (Taglibs, Templates, Controller, ...)
trägt einen durch das [Timing-Modell des
Page-Controller](http://adventure-php-framework.org/Seite/098-Pagecontroller#Chapter-2-1-Ablaufdiagramm)
definierten Teil zur Aufgabe bei.

## Page-Controller Grundlagen

Das Timing-Modell des Page-Controller ist eine der wichtigsten
Meilensteine beim Verständnis des APF-Page-Controller und damit der
Basis des APF. Dies ist jedoch nur dann wichtig, wenn der Entwickler
eigene Taglibs erstellen möchte. Es ist insbesondere dann wichtig, wenn
die Daten, die durch unterschiedliche Taglibs und Controller verarbeitet
werden gemeinsam in den oben genannten Phasen genutzt werden sollen.

Die Diskussion im Foren-Thread [Registry in
taglib](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=333)
bietet neben dieser Seite ein sehr schönes praktisches Beispiel des
Timing-Modells.

Wie im Kapitel [Beschreibung des Page-Controller
Timing-Modells](http://adventure-php-framework.org/Seite/098-Pagecontroller#Chapter-2-2-Beschreibung)
beschrieben ist, wird der Dokumenten-Baum zunächst ausgehend von einem
Basis-Template aufgebaut. Beinhaltet dieses nur einen statischen Text,
so besteht der Dokumenten-Baum aus einem **Page**-Dokument und einem
einzigen Kind-Knoten - dem *initialen Root-Dokument*. Sofern das
Basis-Template weitere "Instruktionen" in Form von Taglibs beinhaltet
(z.B.
[<core:importdesign />](http://adventure-php-framework.org/Seite/046-Standard-TagLibs#Chapter-1-2-Importdesign)),
so wird das in der Tag-Definition benannte Template als weiteres Kind im
Baum eingehangen. Besitzt dieses ebenfalls weitere Tags der genannten
Form, setzt sich der Aufbau des Baumes entsprechend der vorangegangenen
Methode fort.

Bei der Verarbeitung der Tag-Definitionen durch den Page-Controller wird
jeder Tag als Dokument-Knoten im Baum repräsentiert und ist später über
die entsprechenden Einstiegs-Punkte adressierbar. Dies ist insbesondere
für das
[Templating](http://adventure-php-framework.org/Seite/046-Standard-TagLibs#Chapter-2-3-Template)
und den
[Formular-Support](http://adventure-php-framework.org/Seite/113-Formulare)
des APF die Basis der Funktionalität. Der Aufbau des Baumes kann - wie
bereits angedeutet - durch die Implementierung einer eigenen Taglib
beeinflusst werden,
[Document-Controller](http://adventure-php-framework.org/Seite/006-Controller)
sind vornehmlich für die Steuerung der Ausgabe zuständig.

Jede Taglib besitzt - ausgehend von der Klasse **APFObject** folgende
Methoden, die zur Beeinflussung des Baum-Aufbaus einesetzt werden
können:

-   onParseTime()
-   onAfterAppend()

Die Methode *onParseTime()* wird ausgeführt, wenn der Tag vom
Page-Controller analysiert wird, *onAfterAppend()* dann, wenn bereits
alle Knoten des aktuellen Templates analysiert sind. Erstere sollte
daher für Initialisierungs-Aufgaben eines Tags genutzt werden, zweitere
für Aufgaben, die umliegende Knoten benötigen. Die umliegenden Knoten
können über die Klassen-Variablen *$this-&gt;__ParentObject* (Referenz
auf den Vater-Knoten) und *$this-&gt;__Children* (Kind-Knoten)
adressiert werden. Sofern es nötig ist, auf die Kinder des Vater-Knotens
zuzugreifen, kann dies mit
*$this-&gt;__ParentObject-&gt;getChildren()* erledigt werden. Weitere
Methoden sind der API-Dokumentation des jeweiligen Releases zu
entnehmen.

Für den Eingriff in die Gestaltung der Transformation dienen die
Methoden *transform()* (Taglib) und *transformContent()*
(Document-Controller). Innerhalb der Taglib muss dabei - sofern die
Methode *transform()* implementiert wird - auf die umliegenden Knoten
eingegangen werden. Dies ist insbesondere wichtig, wenn der aktuelle
Knoten Kind-Knoten aufweist. Sofern notwendig, müssen diese dann
ebenfalls transformiert werden. Innerhalb eines Document-Controller wird
nur Logik ausgeführt, die sich unmittelbar auf den aktuellen Knoten
beziehen. Der Rest wird vom Page-Controller übernommen.

## Timing-Modell

### Analyse-Phase

Das Timing-Modell des Page-Controller sieht vor, dass beim Aufbau des
Baumes zunächst der Konstruktor einer Taglib aufgerufen wird. Hier
stehen noch keine Informationen dafüber zur Verfügung, mit welchen
Parametern das Tag definiert wurde.

Anschließend werden die Parameter der Tag-Definition, aktuelle Context
und die aktuelle Sprache injiziert. Diese stehen also beim
darauffolgenden Ausführen der Methode *onParseTime()* zur Verfügung.
Dies ist insbesondere für den Zugriff auf Konfigurationen wichtig.

Nach dem Initialisieren eines DOM-Knotens (=Instanz einer Taglib) und
nachdem alle Knoten in den DOM-Baum eingehangen sind, wird für alle
Kind-Knoten die Methode *onAfterAppend()* ausgeführt. Innerhalb dieser
Funktion steht nun der komplette DOM-Baum zum Zufriff zur Verfügung. Es
bietet sich daher an, diese Methode für Operationen zu nutzen, die
andere Knoten benötigen.

### Transformations-Phase

In der nächsten Phase beginnt der Page-Controller damit, den DOM-Baum
rekursiv zu druchlaufen und die einzelnen Konten in ihre
HTML-Entsprechung zu transformieren. Hierzu wird zunächst ein relevanter
Document-Controller ausgeführt und dann die Transformation der
Kind-Knoten angestossen. Das Ergebnis ist am Schluss ein vollständiges
HTML-Gerüst, das aus den einzelnen DOM-Knoten zusammengesetzt wurde.

Soll der Inhalt eines Knotens durch einen Document-Controller
beeinflusst werden, muss dieser die Methode *transformContent()*
implementieren. Innerhalb einer Taglib ist hierfür die Methode
*transform()* relevant. Wie oben bereits angedeutet, muss in dieser
Methode auf die umliegenden Knoten (vor allem: Kind-Knoten) eingegangen
werden. Findest das nicht statt, werden die Kind-Knoten u.U. nicht
ausgegeben.

### Zeitliche Abhängigkeiten

Zwischen den einzelnen Ausführungen der genannten Methoden ergeben sich
aus der Struktur des Page-Controller dedizierte zeitliche
Abhängigkeiten. Das bedeutet:

-   *onParseTime()*-Methoden von Taglibs, die zuerst geparst werden,
    werden auch zuerst ausgeführt.
-   *onAfterAppend()*-Methoden von Taglibs, die zuerst geparst werden,
    werden auch zuerst ausgeführt.
-   *transform()*-Methoden werden nach der Struktur des Baumes uns nach
    der Reihenfolge aufgerufen, in der die Knoten initialisiert wurden.
-   Nachdem der Baum rekursiv transformiert wird, können Teil-Bäume vor
    anderen bereits vollständig transformiert werden.

Eine genaue Auskunft darüber, welche Elemente wann transformiert werden,
kann der
[BenchmarkTimer](http://adventure-php-framework.org/Seite/004-Benchmark)
geben. Dieser zeichnet - sofern er nicht deaktiviert ist - den Baum so
auf, wie er auch tatsächlich im Speicher existiert. Ein Beispiel kann
auf dem Screenshot im Thread [Registry in
taglib](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=333#p2834)
entnommen werden.

Wegen der zeitlichen Abhängigkeiten wird deshalb empfohlen, von mehreren
DOM-Knoten gemeinsam genutzte Informationen vor dem Erzeugen der Seite
mit Hilfe des
[Front-Controller](http://adventure-php-framework.org/Seite/012-Frontcontroller)
zu erzeugen oder sofern die Informationen erst zur Zeit der
Transformation benötigt werden, mit innerhalb der **Analyse-Phase**.