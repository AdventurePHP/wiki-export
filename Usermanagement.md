## Einleitung

Das Modul **usermanagement** ist die technische Implementierung eines
Konzeptes für ein allgemeingültiges Benutzer-Management, das den
Anspruch hat, in jeder Applikation wiederverwendet werden zu können.
Durch die Objekt-Struktur können neben Benutzer, Gruppen und deren
gegenseitige Zuordnung auch Funktions- und Sichtbarkeits-Berechtigungen
verwaltet werden.

Üblicherweise werden Sichtbarkeits-Berechtigungen in Applikationen über
Rollen abgebildet. Diese sind im klassischen Sinn jedoch Elemente, die
Funktions-Berechtigungen definieren. Ein Analogin: eine Person erhält
über seine gesellschaftliche Rolle Befugnisse innerhalb der
Gesellschaft. Welche Elemente der Person sichtbar sind hängt zwar auch
an der Rolle, jedoch ist es in komplexen Applikationen notwendig
zwischen reiner Sichtbarkeit und den Aktionen, die ein Benutzer mit
einem sichtbaren Objekt "anstellen" kann zu unterscheiden.

## Domänen-Modell

### Benutzer und Gruppen

Benutzer repräsentieren Personen, die sich an einer Anwendung anmelden
können. Gruppen dienen originär zur Gruppierung von Benutzern um diesen
Sichtbarkeits-Berechtigungen auf Applikations-Objekte zuweisen zu
können. Sie können jedoch ebenso für die Regelung von Zugriffen in
einfachen Applikationen eingesetzt werden. Ebenso möglich ist es,
Gruppen als Instrument der Funktions-Berechtigungen einzusetzen. Die
beiden letzten Vorgehensweise sind jedoch ausdrücklich keine Empfehlung,
sondern lediglich eine Möglichkeit, wie das Modul verwendet werden kann.

### Rollen, Permission-Sets und Permissions

Rollen dienen der Zuordnung von Funktions-Berechtigungen zu einem
Benutzer. Mit einer definierten Rolle hat ein Benutzer beispielsweise
die Möglichkeit einen News-Eintrag zu bearbeiten, nicht aber zu
veröffentlichen oder zu löschen. Die konkreten Berechtigungen werden
über Permissions angelegt. Eine Permission definiert sich über einen
Schlüssel und einen Wert, der innerhalb der Applikation als Kenner für
die Freischaltung oder Verweigerung einer Berechtigung genutzt werden
kann. Permission-Sets gruppieren Permissions, damit gleichartige
Berechtigungen einfacher an Rollen zugewiesen werden können. So kann ein
Permission-Set die Funktions-Berechtigungen für Artikel enthalten ein
anderes die für News-Einträge relevanten Permissions.

### Sichtbarkeits-Berechtigungen

Mit Sichtbarkeits-Berechtigungen wird festgelegt, welcher Benutzer
und/oder welche Gruppe Zugriff auf ein Objekt einer Applikation besitzt.
Da das Usermanagement für jedwede Anwendungen eingesetzt werden können
soll werden innerhalb des Moduls Stellvertreter-Objekte gepflegt, die
Objekt-Typen und -IDs der realen Objekte einer Applikation
repräsentieren. So ist eine allgemeingültige Einsatzmöglichkeit
geschaffen, die innerhalb des Moduls selbst für eine einfache
Integration in die mitgelieferte Verwaltungs-GUI schafft.

## Anwendung Sichtbarkeits-Berechtigungen

Sichtbarkeits-Berechtigungen regeln den Zugriff auf ein Objekt. Hat ein
Benutzer oder ein Benutzer über seine Gruppe die
Sichtbarkeits-Berechtigung auf ein Objekt, so kann er dieses in einer
Oberfläche sehen. Welche Aktionen für den Benutzer mit dem Objekt
verknüpft sind, regelt die im nächsten Kapitel beschriebene
Funktions-Berechtigung.

### Konzept

Das Konzept der Funktions-Berechtigung ist im Usermanagement-Modul über
Stellvertreter-Objekte gelöst, die zum einen einen konkreten Objekt-Typ
einer Anwendung über eine Beziehung zu einem
Sichtbarkeits-Berechtigungs-Typ repräsentieren und zum anderen
Beziehungen zu Benutzern und Gruppen ausprägen.

Dadurch ist es möglich mit dem Umgt-Modul zu definieren, welcher
Benutzer auf welches Objekt eines definierten Typs Zugriff hat. Implizit
kann eine Berechtigung auch auf der Ebene von Gruppen vergeben oder eine
Mischung genutzt werden.

### Auslesen von Sichtbarkeits-Berechtigungen

Eine Sichtbarkeits-Berechtigung ist immer über zwei Elemente eindeutig:
dem Typ des Applikations-Objekts (News, Artikel, ...) und dem Benutzer
oder der Gruppe. Üblicherweise wird der Benutzer in personalisierten
Anwendungen als zentrales Element in einem Model mitgeführt. Daher
bietet die Business-Komponente **UmgtManager** eine Methode, die sowohl
die expliziten Berechtigungen des Benutzers selbst als auch die seiner
Gruppe zurück gibt.

Das folgende Beispiel zeigt das Auslesen aller für den Benutzer
sichtbaren Objekte:

``` php
$uM = &$this->getUmgtManager();
$type = $uM->loadVisibilityDefinitionTypeByName('Gallery');
$proxies = $uM->loadAllVisibilityDefinitions($user,$type);
```

Die Methode *getUmgtManager()* versteckt in diesem Beispiel - und allen
folgenden - die Erzeugung des **UmgtManager**. Details hierzu können
unter [Anwendung der
Businesskomponente](http://adventure-php-framework.org/Seite/095-Usermanagement#Chapter-3-2-Businesskomponente)
eingesehen werden.

Sofern nur die sichtbaren Objekte für den Benutzer oder einer Gruppe
ausgelesen werden soll, kann dies per

``` php
$uM = &$this->getUmgtManager();
$type = $uM->loadVisibilityDefinitionTypeByName('Gallery');

// alle Berechtigungen einer definierten Gruppe
$group = $uM->loadGroupByName('Administratoren'),
$groupProxies = $uM->loadVisibilityDefinitionsByGroup($group,$type);

// alle Berechtigungen eines Benutzers
$userProxies = $uM->loadVisibilityDefinitionsByUser($user,$type);

// alle Berechtigungen, die für einen definierten Typ vorhanden sind
$allProxies = $uM->loadVisibilityDefinitionsByType($type);
```

### Erzeugen von Sichtbarkeits-Berechtigungen

Das Erzeugen von Sichtbarkeits-Berechtigungen lässt sich sehr einfach
über den *UmgtManager* abbilden:

``` php
$uM = &$this->getUmgtManager();
$type = $uM->loadVisibilityDefinitionTypeByName('Gallery');

// Stellvertreter-Objekt erzeugen und konfigurieren
$proxy = new GenericDomainObject('AppProxy');
$proxy->setProperty('AppObjectId',$id);

$uM->createVisibilityDefinition($type,$proxy,array($user));
```

*$id* ist dabei die ID des Objekts der Applikation für das eine
Berechtigung eingerichtet werden soll.

Sofern die Gruppe(n) des Benutzers für die Zuordnung genutzt werden
soll, kann das wie folgt erledigt werden:

``` php
$uM = &$this->getUmgtManager();
$type = $uM->loadVisibilityDefinitionTypeByName('Gallery');

$proxy = new GenericDomainObject('AppProxy');
$proxy->setProperty('AppObjectId',$id);

// Berechtigungen auf alle Gruppen des Benutzers
$groups = $uM->loadGroupsWithUser($user);
$uM->createVisibilityDefinition($type,$proxy,array(),$groups);

// Berechtigung für eine defizierte Gruppe
$singleGroup = $uM->loadGroupByName('Administratoren');
$uM->createVisibilityDefinition($type,$proxy,array(),array($singleGroup));
```

### Verwalten von Sichtbarkeits-Berechtigungen

Die Berechtigungen können entweder direkt über die
[Verwaltungs-GUI](http://adventure-php-framework.org/Seite/095-Usermanagement#Chapter-3-1-Backend)
administriert werden oder über den *UmgtManager* direkt in der
Anwendung. Hierzu stehen die Methoden

-   *saveVisibilityDefinitionType()*
-   *deleteVisibilityDefinitionType()*
-   *deleteVisibilityDefinition()*

zur Verfügung. Explizite Funktionen zum Update einer *visibility
definition* gibt es nicht, da die Methoden
*createVisibilityDefinition()* und *deleteVisibilityDefinition()* auch
für das Hinzufügen oder Entfernen von einzelnen Berechtigungen genutzt
werden können.

Wurde für ein Objekt A einer Berechtigung für Benutzer B und C angelegt,
so kann durch Aufruf der Methode *deleteVisibilityDefinition()* und
Benutzer B als Parameter die Berechtigung von B granular aufgelöst
werden. Gleiches gilt für die Zuordnung von Berechtigungen. Sollte D
noch keine Berechtigung besitzten, reicht es diesen als Argument an
*createVisibilityDefinition()* zu übergeben um die fehlende Berechtigung
zu erzeugen.

## Anwendung Funktions-Berechtigungen

... to be continued ...