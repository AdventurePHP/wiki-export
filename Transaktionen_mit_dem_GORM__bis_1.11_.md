Der
[GenericORMapper](http://adventure-php-framework.org/Seite/063-Generischer-OR-Mapper)
(GORM) bietet aktuell (=bis einschließlich Version 1.11) keine
Möglichkeit, Transaktionen direkt unterstützen. Hierzu gibt es eine
einfache Möglichkeit, dieses Verhalten mit Hilfe der
Datenbank-Verbindung, die der GORM nutzt, nachzustellen. Hierzu ist
folgender Code notwendig:

``` php
import('modules::genericormapper::data','GenericORMapperFactory');
$ormf = $this->__getServiceObject('modules::genericormapper::data','GenericORMapperFactory');
$orm = $ormf->getGenericORMapper({CONFIG_NAMESPACE},{CONFIG_NAME_AFFIX},{CONNECTION_NAME},{SERVICE_OBJECT_TYPE});
$dbConn = $orm->get('DBDriver');

// Transaktion starten
$dbConn->executeTextStatement('START TRANSACTION;');

// Business-Aktion durchführen
$foo = /* komplexe Objekt-Struktur */
$orm->saveObject($foo);

// Transaktion beenden
$dbConn->executeTextStatement('COMMIT;');
```

Dies ist jedoch nur ein Hilfkonstrukt, da der GORM selbst keine
Transaktion unterbricht, es sei denn, eine Datenbank-Exception tritt auf
(z.B. "*unique key constraint*"-Fehler). Fehlerbehandlung ist auch beim
Anlegen von Objekten weniger relevant als beim Ändern und Löschen. Vor
allem bei letzterem wird ein Fehler geworfen, wenn Objekte gelöscht
werden, die weitere komponieren.