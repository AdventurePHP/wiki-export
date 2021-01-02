## Einleitung

Der
[GORM](http://adventure-php-framework.org/Seite/063-Generischer-OR-Mapper#Chapter-4-1-Erzeugen-einer-Instanz)
kann auch mit dem
[DIServiceManager](http://adventure-php-framework.org/Seite/107-Services#Chapter-4-DIServiceManager)
erzeugt werden.

Dies hat den Vorteil, dass die explizite Initialisierung nicht mehr im
Quellcode stattfinden muss, sondern in eine Konfiguration ausgelagert
werden kann. Weitere Vorteile ergeben sich für die Testbarkeit einer
Komponente, die auf dem GORM basiert und die Entkoppelung der
Komponenten wichtig.

Die folgenden Kapitel zeigen, wie die Erzeugung und Initialisierung mit
dem DIServiceManager des [Generische
O/R-Mapper](http://adventure-php-framework.org/Seite/063-Generischer-OR-Mapper)
technisch funktioniert.

## Erzeugen des GORM

Für die Erzeugung des O/R-Mapper wurd bisher eine Factory angeboten, die
den Mapper mit den relevanten Informationen des Frameworks und der
gewünschten Konfiguration versorgt (*GenericORMapperFactory*). Diese ist
ab dem Release 2.0 als veraltet gekennzeichnet.

Das Konzept der *[dependency
injection](http://de.wikipedia.org/wiki/Dependency_Injection)* besagt,
dass nicht mehr eine aufrufende Komponente für die Initialisierung der
Abhängigen zuständig ist, sondern ein Mediator, bzw. implizit der
Service selbst. Hierzu wird in der JAVA-Welt oft ein
Dependency-Injection-Container - wie in Spring enthalten - genutzt. Das
APF besitzt für diese Aufgabe den **DIServiceManager**, der
Abhängigkeiten zwischen Services auflösen und korrekt konfigurierte
Services zur Verfügung stellen kann.

Der **DIServiceManager** erwartet hierzu eine Konfiguration, die den
Service auf einer Meta-Ebene beschreibt und namentlich eindeutig
kennzeichnet. Ein Service zeichnet sich im allgemeinen durch ein Mapping
auf eine konkrete Implementierung und statische sowie dynamische
Konfigurationen aus. Dynamische Konfigurationen sind solche, die einen
abhängigen Service in einen anderen Service injizieren. So kann
beispielsweise ein Datenbank-Adapter in einen Business-Service injiziert
werden, damit dieser auf die relevante Datenbank-Resource Zugriff hat.

Im Fall des GORM ist es notwendig eine Konfiguration zu injizieren, die
die Initialisierung des Mappers übernimmt. Diese umfasst

-   Konfigurations-Namespace
-   Suffix der Konfigurations-Datei
-   Name der zu nutzenden Datenbank-Verbindung
-   Anweisungen für den Datenbank-Treiber

Der GORM kann vollständig per DI-Container (siehe
[DIServiceManager](http://adventure-php-framework.org/Seite/107-Services#Chapter-4-4-4-Initialisierung-eines-komplexen-Service))
initialisiert werden. Hierzu ist die folgende Konfiguration notwendig:

``` ini
[GORM]
servicetype = "SESSIONSINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
setupmethod = "setup"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "APF\modules\usermanagement\data"
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "umgt"
conf.db.method = "setConnectionName"
conf.db.value = "Umgt"
conf.debug.method = "setLogStatements"
conf.debug.value = "false"
```

Die "setupmethod" wird einmalig pro Erzeugung des Objekts ausgeführt um
das Objekt zu initialisieren.

## Weitere Services

Sofern der GORM als Basis-Service in einer weiteren Komponente (hier:
*MyAppManager*) dient, kann er sehr einfach per

``` ini
[MyBusinessService]
servicetype = "SINGLETON"
class = "VENDOR\..\MyAppManager"
init.gorm.method = "setMapper"
init.gorm.namespace = "..."
init.gorm.name =  "GORM"
```

in diesem Service injiziert werden.

## Zusätzliche Mapping-Konfigurationen

Ein weiteres Feature des GORM ist die Möglichkeit, on-the-fly weitere
Mapping-Konfigurationen hinzuzufügen um ein Basis-Set an
Objekt-Definitionen für bestimmte Anwendungsfälle einfach erweitern zu
können.

Für diesen Anwendungsfall steht der selbe Mechanismus wie oben
beschrieben zur Verfügung - mit dem Unterschied, dass die dynamische
Mapping-Konfiguration von der Klasse
**GenericORMapperDIDomainObjectsConfiguration** (dynamisch geladen vom
GenericORRelationMapper) repräsentiert wird.

Um einen GORM-Service mit einer zusätzlichen Mapping-Konfiguration
auszustatten ist folgende Service-Konfiguration notwendig:

``` ini
[GORM]
servicetype = "SINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
setupmethod = "setup"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "..."
conf.conn.method = "setConnectionName"
conf.conn.value = "..."
conf.debug.method = "setDebugMode"
conf.debug.value = "true|false"

init.map.method = "addDIMappingConfiguration"
init.map.namespace = "..."
init.map.name =  "GORM-CONFIG-MAPPING"


[GORM-CONFIG-MAPPING]
servicetype = "NORMAL"
class = "APF\modules\genericormapper\data\GenericORMapperDIMappingConfiguration"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigAffix"
conf.affix.value = "..."
```

## Zusätzliche Beziehungs-Konfigurationen

Ein weiteres Feature des GORM ist die Möglichkeit, on-the-fly weitere
Beziehungs-Konfigurationen hinzuzufügen um ein Basis-Set an
Objekt-Definitionen für bestimmte Anwendungsfälle einfach erweitern zu
können.

Dies funktioniert analog zur dynamischen Mapping-Konfiguration über die
Klasse **GenericORMapperDIRelationConfiguration**:

``` ini
[GORM]
servicetype = "SINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
setupmethod = "setup"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "..."
conf.conn.method = "setConnectionName"
conf.conn.value = "..."
conf.debug.method = "setDebugMode"
conf.debug.value = "true|false"

init.rel.method = "addDIRelationConfiguration"
init.rel.namespace = "..."
init.rel.name =  "GORM-CONFIG-RELATION"


[GORM-CONFIG-RELATION]
servicetype = "NORMAL"
class = "APF\modules\genericormapper\data\GenericORMapperDIRelationConfiguration"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigAffix"
conf.affix.value = "..."
```

## Zusätzliche Domänen-Objekt-Konfigurationen

Neben der Definition der Objekte und Beziehungen beherrscht der GORM die
Definition von Domänen-Objekt-Klassen, um die definierten Objekte in
konkreten Datentypen statt einem *GenericDomainObject* zu
repräsentieren. Hierzu bietet der GORM die Möglichkeit, on-the-fly
weitere Domänen-Objekt-Konfigurationen hinzuzufügen um ein Basis-Set an
Domänen-Objekt-Definitionen für bestimmte Anwendungsfälle einfach
erweitern zu können.

Dies funktioniert analog zur dynamischen Beziehungs-Konfiguration über
die Klasse **GenericORMapperDIDomainObjectsConfiguration**:

``` ini
[GORM]
servicetype = "SINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
setupmethod = "setup"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "..."
conf.conn.method = "setConnectionName"
conf.conn.value = "..."
conf.debug.method = "setDebugMode"
conf.debug.value = "true|false"

init.do.method = "addDIDomainObjectsConfiguration"
init.do.namespace = "..."
init.do.name =  "GORM-CONFIG-ADDITIONAL-SERVICEOBJECTS"


[GORM-CONFIG-ADDITIONAL-SERVICEOBJECTS]
servicetype = "NORMAL"
class = "APF\modules\genericormapper\data\GenericORMapperDIDomainObjectsConfiguration"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigAffix"
conf.affix.value = "..."
```

## Beispiel

Das folgende Beispiel zeigt die Konfiguration eines Business-Services,
der eine GORM-Instanz mit erweiterterter Konfiguration nutzt:

``` ini
[Manager]
servicetype = "SINGLETON"
class = "VENDOR\..\MyAppManager"
init.gorm.method = "setMapper"
init.gorm.namespace = "..."
init.gorm.name = "GORM"

[GORM]
servicetype = "SINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
setupmethod = "setup"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "..."
conf.conn.method = "setConnectionName"
conf.conn.value = "..."
conf.debug.method = "setDebugMode"
conf.debug.value = "true|false"

init.rel.method = "addDIRelationConfiguration"
init.rel.namespace = "..."
init.rel.name = "GORM-CONFIG-RELATION"
init.map.method = "addDIMappingConfiguration"
init.map.namespace = "..."
init.map.name = "GORM-CONFIG-MAPPING"


[GORM-CONFIG-MAPPING]
servicetype = "NORMAL"
class = "APF\modules\genericormapper\data\GenericORMapperDIMappingConfiguration"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigAffix"
conf.affix.value = "..."


[GORM-CONFIG-RELATION]
servicetype = "NORMAL"
class = "APF\modules\genericormapper\data\GenericORMapperDIRelationConfiguration"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigAffix"
conf.affix.value = "..."
```

Der Aufruf des Business-Services erfolgt innerhalb eines Controllers -
bzw. jeder Instanz der Klasse **APFObject** wie folgt:

``` php
$manager = &$this->getDIServiceObject('...','Manager');
```

Der Namespace ist gemäß der Ablage der Konfigurations-Datei zu wählen.
Details können dem Kapitel
[Konfiguration](http://adventure-php-framework.org/Seite/134-Konfiguration)
der APF-Dokumentation entnommen werden.

Soll die Datenbank-Verbindung des O/R-Mappers ebenfalls über den
Service-Container erzeugt werden ergänzt sich das Beispiel wie folgt:

``` ini
[Manager]
servicetype = "SINGLETON"
class = "VENDOR\..\MyAppManager"
init.gorm.method = "setMapper"
init.gorm.namespace = "..."
init.gorm.name = "GORM"

[GORM]
servicetype = "SINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
setupmethod = "setup"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "..."
conf.conn.method = "setConnectionName"
conf.conn.value = "..."
conf.debug.method = "setDebugMode"
conf.debug.value = "true|false"

init.db.method = "setDbDriver"
init.db.namespace = "..."
init.db.name = "DATABASE"

init.rel.method = "addDIRelationConfiguration"
init.rel.namespace = "..."
init.rel.name =  "GORM-CONFIG-RELATION"

init.map.method = "addDIMappingConfiguration"
init.map.namespace = "..."
init.map.name =  "GORM-CONFIG-MAPPING"


[GORM-CONFIG-MAPPING]
servicetype = "NORMAL"
class = "APF\modules\genericormapper\data\GenericORMapperDIMappingConfiguration"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigAffix"
conf.affix.value = "..."


[GORM-CONFIG-RELATION]
servicetype = "NORMAL"
class = "APF\modules\genericormapper\data\GenericORMapperDIRelationConfiguration"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigAffix"
conf.affix.value = "..."

[GORM-DATABASE]
servicetype = "SINGLETON"
class = "APF\core\database\MySQLiHandler"
setupmethod = "setup"

conf.host.method = "setHost"
conf.host.value = "..."
conf.name.method = "setDatabaseName"
conf.name.value = "..."
conf.user.method = "setUser"
conf.user.value = "..."
conf.pass.method = "setPass"
conf.pass.value = "..."
```

## Konfiguration der Datei-Endung

Es gibt zwei Möglichkeiten, die Datei-Endung für GORM-Konfigurationen
(*\*_objects.ini*, *\*_relations.ini*, *\*_serviceobjects.ini*) zu
definieren:

### Indirekte DI-Konfiguration

Bei Erzeugung des GORM über den **DIServiceManager** (siehe oben) kann
bei der Definition der Konfiguration (*GenericORMapperDIConfiguration*)
darauf geachtet werden, dass die Service-Sektion die Datei-Endung via

``` ini
[GORM-CONFIG]
...
conf.ext.method = "setConfigFileExtension"
conf.ext.value = "orm"
```

initialisiert wird. Die Information der Datei-Endung wird von dort dann
in den Mapper injiziert.

### Direkte DI-Konfiguration

Sofern der GORM direkt über den **DIServiceManager** erzeugt wird kann
die entsprechende Methoden, die die Konfiguration injizieren auch direkt
genutzt werden.

Zur Definition eines GORM-Service ist folgende Konfiguration notwendig:

``` ini
[GORM]
servicetype = "SINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
conf.ext.method = "setConfigFileExtension"
conf.ext.value = "orm"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "..."
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "..."
conf.conn.method = "setConnectionName"
conf.conn.value = "..."
conf.debug.method = "setLogStatements"
conf.debug.value = "true|false"
```

Dabei ist darauf zu achten, dass die Reihenfolge der Initialisierung
eingehalten ist. Wird die Methode *setConfigFileExtension()*
beispielsweise nicht vor *setConfigNameAffix()* aufgerufen, wird die
Standard-Endung (**.ini**) bei der Initialisierung genutzt.