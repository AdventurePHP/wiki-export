<translate> Dieser Eintrag soll häufig auftretende
Implementierungsfehler aufzuzeigen und entsprechende Lösungen dazu
bereitstellen.

## Fehler: Unknown table 'ent_xyz' in on clause

Diese Meldung tritt in folgenden Fall auf: Beim Laden von Objekten über
eine GenericCriterionObject-Bedingung ist ein "falscher"
Beziehungsschlüssel angegeben.

Die Ursache des Fehlers ist, dass in der JOIN-Bedingung eine Tabelle
referenziert wird, die nicht zu den vorhandenen Beziehungen passt. Es
muss deshalb immer darauf geachtet werden, welche Objekte mit der
Methode

``` php
loadNotRelatedObjects()
```

geladen werden, so dass die Beziehungsdefinition im
GenericCriterionObject auf diesen Objekt-Typ passt.

**Beispiel (aus dem Usermanagement-Modul):**

Wird statt

``` php
$crit->addRelationIndicator('Application2User', $app);
return $orm->loadNotRelatedObjects($role, 'Role2User', $crit);
```

die Definition des GenericCriterionObject mit

``` php
$crit->addRelationIndicator('Application2Role', $app);
return $orm->loadNotRelatedObjects($role, 'Role2User', $crit);
```

definiert, wird der MySQL-Fehler \#1109 -

``` html4strict
Unknown table 'ent_role in on clause
```

ausgegeben.

## Fehler: Undefined index: xyz

**Fehlerursache 1:** Diese Meldung kann in folgendem Fall auftreten:
Eine Änderung an der Konfiguration der \*_objects.ini oder
\*_relations.ini, wobei der GenericORMapper als SESSIONSINGLETON
erzeugt wurde. Da dies häufig während der Entwicklung einer Anwendung
auftritt, gibt es die Möglichkeit, den GenericORMapper als SINGLETON zu
erzeugen:

``` php
$factory = &$this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory', APFService::SERVICE_TYPE_SINGLETON);
```

Eine weitere Möglichkeit ist das Löschen bzw. Neuanlegen der Session.

**Fehlerursache 2:** Diese Meldung kann in folgendem Fall auftreten: Das
Projekt besteht aus mehreren \*_objects.ini und \*_relations.ini -
Konfigurationsdateien. Für jede Konfiguration gibt es einen seperaten
Manager, welcher für seine Aufgaben zuständig ist. Greift der Manager
nun jedoch auf bereits existierende Konfigurationen eines anderen
Managers zu, kommt es zu dem genannten Fehler. Abhilfe schafft hier,
einfach die notwendigen Definitionen aus den .ini-Konfigurationen in die
andere Konfiguration zu kopieren. Folgender Foren-Thread ist dazu auch
sehr aufschlussreich: [The object name "Application" does not exist
...](http://forum.adventure-php-framework.org/viewtopic.php?f=6&t=640)

Man muss aber nicht sämtliche Objekte und Beziehungen in die anderen
Konfigurationen kopieren. Es gibt auch die Möglichkeit, den
GenericORMapper mit mehreren Konfigurationen zu initiieren:

``` php
// Zusaetzliche Objekt-Definitionen hinzuladen
$oRM->addMappingConfiguration('APF\modules\usermanagement', 'umgt_2');

<!--T:9-->
// Zusaetzliche Beziehungs-Definitionen hinzuladen
$oRM->addRelationConfiguration('APF\modules\usermanagement', 'umgt_2');
```

Siehe dazu auch die Doku: [GenericORMapper 6. Erweiterung des Mapping-
und
Relation-Table](http://adventure-php-framework.org/Seite/063-Generischer-OR-Mapper#Chapter-6-Erweiterung-Mapping-und-Relation-Table)
</translate> <languages />