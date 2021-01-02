Durch verändern des *ConfigurationProvider* kann man seine ganz
persönliche Konfigurationsstruktur erzeugen.

Der Anlass warum die folgenden Provider geschrieben wurden war, dass man
die Konfiguration von einem Kontext leichter in einen anderen ändern
kann. (Um z.B. eine zweite Seite anzulegen bei der dann nur einzelne
Details verändert wurden.)

## Vorgehensweise

Man erweitert einfach jene Provider die man benötigt und überschreibt
die gewünschten Funktionen. In diesem Beispiel wurde nur der Pfad
verändert.

``` php
namespace VENDOR\config\provider;

use APF\core\configuration\ConfigurationException;
use APF\core\configuration\provider\ini\IniConfigurationProvider;
use APF\core\loader\RootClassLoader;
use Exception;

class CMSIniConfigurationProvider extends IniConfigurationProvider {

   protected function getFilePath($namespace, $context, $language, $environment, $name) {

      // fallback for missing file extensions (backward compatibility for pre-1.13 config files)
      if (!preg_match('/\.' . $this->extension . '$/i', $name)) {
         $name = $name . '.' . $this->extension;
      }

      // assemble the context
      $contextPath = ($this->omitContext || $context === null) ? '' : '/' . str_replace('::', '/', $context);

      // assemble file name
      $fileName = ($this->omitEnvironment || $environment === null) ? '/' . $name : '/' . $environment . '_' . $name;

      try {
         $classLoader = RootClassLoader::getLoaderByNamespace($namespace);
         $rootPath = $classLoader->getConfigurationRootPath();
         $vendor = $classLoader->getVendorName();
         $fqNamespace = str_replace($vendor . '\\', '', $namespace);
         return $rootPath
         . '/config'
         . $contextPath
         . '/' . str_replace('\\', '/', $fqNamespace)
         . $fileName;
      } catch (Exception $e) {
         throw new ConfigurationException('Class loader root path for namespace "' . $namespace . '" cannot be determined.'
            . ' Please double-check your configuration!', E_USER_ERROR, $e);
      }
   }

}
```

Damit man den neuen Provider auch verwenden kann muss man ihn für die
Dateiendung registrieren. Dazu trägt man am besten in der Bootstrap
Datei folgendes ein:

``` php
use APF\core\configuration\ConfigurationManager;
use VENDOR\config\provider\CMSIniConfigurationProvider;

ConfigurationManager::registerProvider('ini', new CMSIniConfigurationProvider());
```

Sofern man aber zb SQL Statements aus Dateien bezieht muss natürlich
auch dieser Provider geändert werden.

``` php
namespace VENDOR\config\provider;

use APF\core\configuration\ConfigurationException;
use APF\core\database\config\StatementConfigurationProvider;
use APF\core\loader\RootClassLoader;
use Exception;

class CMSStatementConfigurationProvider extends StatementConfigurationProvider {

   protected function getFilePath($namespace, $context, $language, $environment, $name) {

      // fallback for missing file extensions (backward compatibility for pre-1.13 config files)
      if (!preg_match('/\.' . $this->extension . '$/i', $name)) {
         $name = $name . '.' . $this->extension;
      }

      // assemble the context
      $contextPath = ($this->omitContext || $context === null) ? '' : '/' . str_replace('::', '/', $context);

      // assemble file name
      $fileName = ($this->omitEnvironment || $environment === null) ? '/' . $name : '/' . $environment . '_' . $name;

      try {
         $classLoader = RootClassLoader::getLoaderByNamespace($namespace);
         $rootPath = $classLoader->getConfigurationRootPath();
         $vendor = $classLoader->getVendorName();
         $fqNamespace = str_replace($vendor . '\\', '', $namespace);
         return $rootPath
         . '/config'
         . $contextPath
         . '/' . str_replace('\\', '/', $fqNamespace)
         . $fileName;
      } catch (Exception $e) {
         throw new ConfigurationException('Class loader root path for namespace "' . $namespace . '" cannot be determined.'
            . ' Please double-check your configuration!', E_USER_ERROR, $e);
      }
   }

}
```

Anschließend muss er für die Endung "sql" ebenfalls registriert werden.

``` php
use APF\core\configuration\ConfigurationManager;
use VENDOR\config\provider\CMSStatementConfigurationProvider;

ConfigurationManager::registerProvider('sql', new CMSStatementConfigurationProvider());
```