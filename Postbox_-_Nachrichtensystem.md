## Einleitung

Die "Postbox" ist ein flexibles und leicht erweiterbares
Nachrichtensystem, welches sowohl als **gewöhnliches System für private
Nachrichten (zwischen 2 Benutzern)** Einsatz findet, als auch als
**Nachrichtensystem mit Multi-User Unterstützung**. Eine Feature
Übersicht gibt es weiter unten zu sehen.

Es wird hierbei ein "Channel" System verwendet: Jede Konversation wird
als "Channel" geöffnet, und kann beliebig viele Benutzer beinhalten. Es
können auch nachträglich Benutzer hinzugefügt oder entfernt werden.
Antworten auf eine Nachricht werden dem Channel zugeordnet, und jeder,
noch am Gespräch teilnehmende Benutzer, kann die Antworten der anderen
Benutzer lesen (Gruppengespräch). Die Extension liefert KEINE
Benutzeroberfläche (GUI) mit, da ich der Meinung bin, dass diese in
jedem Projekt sehr unterschiedlich sein wird. Jedoch steht im Forum im
[Diskussionsthread](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=584&start=60#p6111)
der Extension ein Testpaket zur Verfügung, welches eine sehr rudimentäre
GUI für das Nachrichtensystem implementiert, an der man sich bei der
eigenen Implementierung der GUI orientieren kann. Eine GUI muss
keinesfalls alle der bereitgestellten Funktionen implementieren, so kann
beispielsweise auf die Ordnerverwaltung, die Blockliste, oder das
hinzufügen neuer Benutzer zu einem Channel,... verzichtet werden.

## Feature-Übersicht

-   Einzel- oder Gruppengespräche
-   Channels
-   Ordnerverwaltung für Nachrichten durch Benutzer
-   Blockliste (geblockte Benutzer können den Blockenden nichtmehr zu
    einem Channel einladen/ihn anschreiben)
-   Empfängerlisten (normalerweise vom User selbst angelegte Listen mit
    Personen denen öfters die selbe Nachricht geschickt werden soll)
-   Laden von Konversationslisten durch Angabe einer Seitenzahl und der
    Angabe wieviele Nachrichten pro Seite angezeigt werden sollen

## Installation & Konfiguration

Die Extension wird ab Version 1.14 Teil des APFs, und sollte sich dann
unter /extensions/postbox/ befinden.

Unter /config/extensions/postbox/ befinden sich Konfigurationsdateien
(.ini) und SQL-Dateien (.sql), die nach
/config/extensions/postbox/\[{Context}\]/ kopiert werden müssen, und
deren ENVIRONMENT (Dateinamenpräfix) angepasst werden muss. (Der
Context-Ordner kann wegfallen, wenn die Konfigurationsprovider
entsprechend der Doku mit setOmitContext() konfiguriert wurden.)

Die "DEFAULT_serviceobjects.ini" beinhaltet eine Definition, wie der
GenericORRelationMapper bezogen werden soll, da dieser als
DIServiceObject bezogen wird, ebenso wie die Postbox, die jedoch über
die Factory bezogen werden muss (dazu später mehr). Dort müssen die zu
verwendende Datenbanksektion, die in /config/core/database/... definiert
wurde, angepasst werden, und, falls der GORM weitere
Objekt-,Beziehungs-, oder Serviceobjektkonfigurationen laden soll, diese
hinzugefügt werden, wie es unter [Erzeugen des GORM mit dem
DIServiceManager](/Erzeugen_des_GORM_mit_dem_DIServiceManager "wikilink")
beschrieben ist. Falls ihr den GORM bereits an anderer Stelle als
DIServiceManager bezieht, kann auf die bereits existierende
Konfiguration zugegriffen werden, indem in der Sektion "*Postbox*" der
"*init.gorm.namespace*" und "*init.gorm.name*" auf Pfad und Name der
bestehende Konfiguration gesetzt wird, die andere Sektion ist in diesem
Fall dann überflüssig.

Anschließend kann mithilfe des GORM Setup- oder Updatetools die
Datenbanktabellen generiert werden. **Desweiteren muss eine Tabelle
"*User*" existieren, die mindestens das Feld "*Username*" beinhaltet.
(z.B. durch Verwendung des Usermanagement-Moduls des APF)**

## Verwendung

Nach der Konfiguration kann die "*PostboxFactory*" überall innerhalb
einer vom APFObject erbenden Klasse (z.B. einem documentcontroller), wie
folgt, bezogen werden:

``` php
/* @var $PostboxFactory PostboxFactory */
$PostboxFactory = $this->getServiceObject('APF\extensions\postbox\biz\PostboxFactory');
```

Die Factory erzeugt anschließend die "*Postbox*", welche die zentrale
Komponente der Extension darstellt, und Benutzerabhängig ist.
Folgendermaßen kann die Postbox dann bezogen werden, wobei **$User** vom
Interface "*GenericORMapperDataObject*" (z.b. ein *GenericDomainObject*)
sein muss, und einem in der Datenbank existierendem Benutzer entsprechen
muss:

``` php
/* @var $Postbox Postbox */
$Postbox = $PostboxFactory->getPostbox($User);
```

*Tipp: Da die Extension von den in 1.14 neu hinzugekommenen
DomainObject-Funktionen des GenericORRelationMapper Gebrauch macht, sind
über den GORM geladene Objekte, welche zur Postbox gehören, automatisch
auch entsprechende DO's, mit dem selben Interface, als wären sie durch
eine Funktion der Extension geladen. Weitere Informationen darüber
findet ihr in der entsprechenden Dokumentation.*

Für eure GUI legt euch einfach entsprechende Templates mit Controllern
an, und verwendet darin die Postbox wie eben beschrieben. Für
Informationen, welche Funktionen die einzelnen Objekte bereitstellen,
schaut euch einfach die API selbiger an.

### Ordnerverwaltung

Wenn gewünscht (also von eurer GUI implementiert) kann der Benutzer
seine Konversationen in Ordnern verwalten. Hierbei gilt zu sagen, dass
standardmäßig alle Nachrichten NICHT in einem Ordner landen. Diese
können als Liste über das "*Postbox*"-Objekt geladen werden. Verschiebt
der Benutzer nun Nachrichten in einen Ordner (der entweder von euch
vorgegebenen, oder ebenfalls vom Nutzer angelegt ist), werden diese
Nachrichten geladen, indem erst der entsprechende Ordner geladen wird.
Dieser stellt dann wiederum Funktionen zur Verfügung um eine Liste der
darin enthaltenen Nachrichten zu laden.

## Erweitern der Extension

Wer Funktionen an einem der Domainobjekte der Postbox verändern oder
ergänzen möchte, kann dies folgendermaßen tun, ohne die im APF
enthaltenen Dateien zu verändern, um bei Updates keine Probleme zu
bekommen:

In der "*DEFAULT_postbox_domainobjects.ini*" kann ein neuer namespace
für einzelne Objekte festgelegt werden. Entweder ihr kopiert euch nun
die betroffenen Objekte von /extensions/postbox/biz/ zu dem selbst
definierten namespace, oder ihr benutzt den ServiceObjectsGenerator, der
dem GORM beiliegt, um diese automatisch neu zu erzeugen dort. Die
Basisfunktionen sind jeweils in einer Klasse ausgelagert, von denen das
jeweilige Objekt erbt. Diese können durch Neudefinition im neuen Objekt
überschrieben werden, und dort können auch neue Funktionen definiert
werden. Der GORM nutzt anschließenden automatisch eure selbst
definierten Objekte. Weitere Informationen hierzu finden sich in der
Doku des GORM.