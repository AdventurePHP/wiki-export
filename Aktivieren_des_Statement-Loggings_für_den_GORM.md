<translate>

## Frage

Wie aktiviere ich das Statement-Logging für den Generic OR Mapper?

## Antwort

### Allgemein

Um das Logging zu aktivieren, muss der 5. Parameter beim Beziehen des
Managers durch die GenericORMapperFactory auf true gesetzt werden.

``` php
$factory = &$this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
$orm = $factory->getGenericORMapper(
    {config_namespace},
    {config_nameAffix},
    {connectionName},
    {ServiceMode},
    {debugMode}
);
```

Wenn {debugMode} auf true gesetzt wird, werden alle Statements geloggt.
Standardmässig wird false gesetzt.

**Im live Betrieb sollte das Logging NICHT aktiviert werden!**

Wenn der GORM an mehreren Stellen im Quelltext bezogen wird, muss der 1.
Mapper mit true initialisiert werden, da er pro Request (oder bei
SESSIONSINGLETON pro Session) nur einmal initialisiert wird. Wenn er als
SESSIONSINGLETON bezogen wird, muss nach dem Umstellen des Parameters
womöglich der Server neu gestartet werden, um die Änderung zu
übernehmen.

### UMGT-Modul

Sollen beispielsweise die Statements des UmgtManagers geloggt werden,
muss die Funktion getORMapper() des UmgtManagers folgendermaßen
überschrieben werden:

``` php
protected function &getORMapper(){
    $factory = &$this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
    return $factory->getGenericORMapper(
                            'APF\modules\usermanagement',
                            'umgt',
                            $this->connectionKey,
                            $this->serviceMode,
                            true
     );
}
```

### Weitere Hinweise

Weitere Hinweise Zur Aktivierung des Loggings bei Verwendung des
**DIServiceManager** zur Erzeugung des GORM finden Sie unter
[1](http://adventure-php-framework.org/Seite/063-Generischer-OR-Mapper#Chapter-4-1-2-Erzeugung-via-DI).
</translate> <languages />