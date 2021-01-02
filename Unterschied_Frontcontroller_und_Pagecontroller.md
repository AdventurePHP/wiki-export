## Frage

PageController, FrontController, DocumentController.. Hilfe überall
diese Controller! Ehrlich gesagt, verstehe ich dieses Konzept überhaupt
nicht, und erst recht nicht wann ich welchen einsetzen soll / muss.

## Antwort

Das Konzept/Design des APF fußt auf mehreren Design-Pattern, die
unterschiedliche Bereiche einer Web-Anwendung adressiert. Die hier
relevanten Pattern sind das **Page-Controller-**Pattern, das
**Front-Controller-Pattern** und das **HMVC-Pattern**.

Der
[Page-Controller](http://adventure-php-framework.org/Seite/098-Pagecontroller)
soll eine allgemeingültig verwendbare Komponente darstellen, die man
dazu einsetzt, einen Request entgegenzunehmen, diesen nach einer immer
gleichen Vorgehensweise zu verarbeiten und entsprechend zu beantworten.
Er soll dir immer wieder kehrende Aufgaben abnehmen, die du sonst bei
jedem neuen Projekt oder jedem neuen Modul wieder programmieren
müsstest.

Der
[Front-Controller](http://adventure-php-framework.org/Seite/012-Frontcontroller)
erweitert den **Page-Controller** um die Möglichkeit, vor der
Verarbeitung des Requests - in der es beim Page-Controller lediglich um
die Generierung der Darstellung einer Webseite oder eines Moduls geht -
weitere Aktionen ausführen zu können. Dies hat den Hintergrund, dass du
oft vor dem Erzeugen der Darstellung einer Webseite in den einzelnen
Teilen der Anwendung gemeinsam genutzte Informationen brauchts.
Beispiel: ID der Seite, Sprache, ... Diese können in einer
**Front-Controller-Action** vor dem Start des **Page-Controller**
gesammelt werden und stehen dann beim Erstellen der GUI zur Verfügung.
Bei der APF-Webseite wird hier beispielsweise aus dem Vorhandensein der
Parameter "Page" oder "Seite" die jeweils gewählte Sprache (en oder de)
ermittelt.

Ein
[Document-Controller](http://adventure-php-framework.org/Seite/006-Controller)
ist im Zusammenhang mit dem HMVC-Pattern zu sehen. Dieses Pattern ist
eine Erweiterung des MVC-Pattern und bietet die Möglichkeit, die GUI in
einer Baum-Struktur abzubilden. Jeder dieser Baum-Knoten wird mit seinem
eigenen MVC-Controller - dem Document-Controller - ausgestattet. Der
Document-Controller kümmert sich also um die Darstellung eines
Baum-Knotens. So ein Baum-Knoten ist also ein Document in einer
Baum-Struktur des HMVC-Baumes. Der Baum selbst wird vom Page-Controller
verwaltet, sprich den Baum aufzubauen ist eine Aufgabe, die er
allgemeingültig löst, so dass du dich nicht bei jedem Projekt neu darum
kümmern musst.

Es gibt also im APF in der Tat drei unterschiedliche Controller, von
denen du dich selbst lediglich um einen kümmern musst - den
Document-Controller. Den Page- und Front-Controller kannst du lediglich
konfigurieren. Beim Page-Controller durch die Implementierung von
Taglibs - um das Verhalten eines Baum-Knotens selbst definieren zu
können -, beim Front-Controller durch die Implementierung von eigenen
Actions, die vor dem Start des Page-Controller ausgeführt werden (es
gibt auch noch andere Actions, die zu anderen Zeit-Punkten ausgeführt
werden, das ist aber jetzt noch nicht wichtig).