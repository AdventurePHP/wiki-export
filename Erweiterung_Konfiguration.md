Diese Seite beschreibt eine für das Release 1.13 bereits implementierte
Funktionalität (Stand 07.11.2010). Die offizielle Dokumentation findet
sich unter [Konfiguration (ab Version
1.13)](http://adventure-php-framework.org/Seite/134-Konfiguration).

## Einleitung

Diese Seite beschreibt ein Proposal zur Erweiterung der Konfiguration
des APF. Ziel ist es - wie im Thread [Erweiterung
Konfiguration](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=117)
besprochen - die Flexibilität zu erhöhen und gleichzeitig die
Konfiguration zu vereinfachen. Context- und Umgebungs-abhängige
Konfigurationen werden zum einen nicht immer benötigt, zum anderen soll
ein anderes Ordner-Layout auf einfache Weise umgesetzt werden können.

## Grundsätze

-   Die Konfiguration ist zentraler Bestandteil des APF und die
    ausgelieferten Konfigurations-Komponenten sollen daher wie vor dem
    Release 1.13 agieren.
-   Die Erweiterung/Konfiguration einer Konfigurations-Komponente
    erfolgt auf Basis von Providern (ähnlich dem CacheManager). Diese
    implementieren ein Interface und sind daher sehr leicht
    austauschbar.
-   Aus Gründen der einfacheren Handhabung soll die Konfiguration des
    Providers nicht über den DIServiceManager sondern über die Registry
    vorgenommen werden.
-   Die neue Konfigurations-Komponente(n) sollen mehrere Formate im
    lesenden und schreibenden Zugriff behandeln können.

## Code-Beispiele

``` php
/**
 * defines the configuration object returned by the configuration component.
 * each implementation defines it's own interfaces and implementations but
 * respecting the global definition.
 */
interface Configuration {
}

/**
 * defines the structure of all configuration provider implementations.
 */
interface ConfigurationProvider {

   /**
    * @return Configuration The desired configuration.
    */
   function loadConfiguration(String $namespace, String $context, String $language, String $environment, String $name);

   function saveConfiguration(Configuration $config);

}

abstract class APFObject {

   ...

   protected function loadConfiguration(String $namespace, String $name){

      $provider = Registry::retrieve('apf::core','ConfigurationProvider');
      if($provider === null){
         $provider = new DefaultConfigurationProvider();
      }
      return $provider->loadConfiguration($namespace, $this->getContext(), $this->getLanguage(), Registry::retrieve('apf::core','Environment'), $name);

   }

   ...

}
```

## Offene Punkte

-   Wie werden unterschiedliche Formate abgebildet? Idee: ein globaler
    ConfigurationManager, bei dem Provider für ein Format (Datei-Endung)
    registriert werden können.

## Implementierungs-Beispiel

Die folgenden Code-Blöcke zeigen ein Implementierungs-Beispiel für den
Config-Provider und seine Verwendung.

### Interfaces

``` php
interface ConfigurationProvider {

   function loadConfiguration($namespace, $context, $language, $environment, $name);

   function saveConfiguration($namespace, $context, $language, $environment, $name, XConfiguration $config);
}

interface XConfiguration {

   /**
    * @return string
    */
   function getValue($name);

   /**
    * @return XConfiguration
    */
   function getSection($name);

   function setValue($name, $value);

   function setSection($name, XConfiguration $section);
}
```

### Manager

``` php
class XConfigurationManager {

   /**
    *
    * @var ConfigurationProvider The configuration provider instance.
    */
   private static $PROVIDER = array();

   public static function registerProvider($extension, ConfigurationProvider $provider) {
      self::$PROVIDER[strtolower($extension)] = $provider;
   }

   public static function removeProvider($extension) {
      unset(self::$PROVIDER[strtolower($extension)]);
   }

   public static function displayRegisteredProviders() {
      foreach (self::$PROVIDER as $ext => $provider) {
         echo $ext . ': ' . get_class($provider) . '<br />';
      }
   }

   public static function getConfiguration($namespace, $context, $language, $environment, $name) {
      return self::getProvider($name)->loadConfiguration($namespace, $context, $language, $environment, $name);
   }

   private static function getProvider($name) {
      $extPos = strripos($name, '.');
      if ($extPos === false) {
         throw new InvalidArgumentException('Provider cannot be loaded, because the given configuration "' . $name . '" has no extensions!', E_USER_ERROR);
      }
      $ext = strtolower(substr($name, $extPos + 1));
      if (isset(self::$PROVIDER[$ext])) {
         return self::$PROVIDER[$ext];
      }
      throw new InvalidArgumentException('Provider with extension "' . $ext . '" is not registered!', E_USER_ERROR);
   }

}
```

### Konkrete Implementierung für ini-Dateien

``` php
class IniConfigurationProvider implements ConfigurationProvider {

   private $omitConfigPrefix;

   public function __construct($omitConfigPrefix = false) {
      $this->omitConfigPrefix = $omitConfigPrefix;
   }

   public function loadConfiguration($namespace, $context, $language, $environment, $name) {
      $fileName = $this->getFilePath($namespace, $context, $language, $environment, $name);
      $rawConfig = parse_ini_file($fileName, true);
      $config = new IniConfiguration();
      foreach ($rawConfig as $name => $values) {
         $config->setSection($name, $this->parseSection($values));
      }
      return $config;
   }

   private function parseSection($entries) {
      $config = new IniConfiguration();
      foreach ($entries as $name => $value) {
         $config->setValue($name, $value);
      }
      return $config;
   }

   public function saveConfiguration($namespace, $context, $language, $environment, $name, XConfiguration $config) {
      $fileName = $this->getFilePath($namespace, $context, $language, $environment, $name);
      file_put_contents($fileName, $config);
   }

   private function getFilePath($namespace, $context, $language, $environment, $name) {
      // using APPS__PATH is about 50 times faster than the registry!
      $prefix = $this->omitConfigPrefix ? '' : '/config';
      return APPS__PATH
         . $prefix
         . '/' . str_replace('::', '/', $namespace)
         . '/' . str_replace('::', '/', $context)
         . '/' . $environment . '_' . $name;
   }

}
```

### Anwendung

Konfiguration in der index.php. Wird in der pagecontroller.php
(Bootstrap-Datei) bereits vorbelegt.

``` php
XConfigurationManager::registerProvider('ini', new IniConfigurationProvider());
```

Manuelle Anwendung in einer PHP-Datei:

``` php
$config = XConfigurationManager::getConfiguration(
                'foo::bar', 'test', 'de', Registry::retrieve('apf::core', 'Environment'), 'bar.ini');
```

Anwendung innerhalb einer Klasse, die von APFObject erbt:

``` php
class foo_controller extends base_controller {

   public function transformContent() {
      $config = $this->loadConfiguration('foo::bar', 'bar.ini');

      ...

      $section = $config->getSection('default');
      $section->setValue('name','value');

      $config->setSection('default',$section);
      $this->saveConfiguration('foo::bar', 'bar.ini', $config);
   }

}
```