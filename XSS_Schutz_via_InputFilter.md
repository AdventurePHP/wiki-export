Die
[APF-Formular-Unterstützung](http://adventure-php-framework.org/Seite/113-Formulare)
bietet bereits einige Mechanismen um XSS-Attacken abzuwehren. Diese
wirken jedoch lediglich lokal auf entsprechend konfigurierte
Eingabefelder.

Der folgende APF
[InputFilter](http://adventure-php-framework.org/Seite/137-Filter#Chapter-2-Architektur)
ermöglicht es, potentielle XSS-Fragmente global zu filtern.
Voraussetzung ist jedoch, dass Formular-Inhalte - diese sind zumeist
Ziel eines Angriffs - per **POST** abgesendet werden.

``` php
namespace VENDOR\namespace\of\filter;

class XssProtectionInputFilter implements ChainedContentFilter {

   private $charset;

   public function __construct() {
      $this->charset = Registry::retrieve('APF\core', 'Charset');
   }

   public function filter(FilterChain &$chain, $input = null) {
      if ($_SERVER['REQUEST_METHOD'] == 'POST') {
         foreach ($_POST as $key => $value) {
            if (is_array($value)) {
               $cleanValue = array();
               foreach ($value as $innerKey => $innerValue) {
                  $cleanValue[$innerKey] = $this->replacePotentialXssContent($innerValue);
               }
            } else {
               $cleanValue = $this->replacePotentialXssContent($value);
            }
            $_POST[$key] = $cleanValue;
            $_REQUEST[$key] = $cleanValue;
         }
      }
      $chain->filter($input);
   }

   private function replacePotentialXssContent($input) {
      return strip_tags(html_entity_decode($input, ENT_QUOTES, $this->charset));
   }

}
```

Zur Einbindung bzw. Aktivierung des Filters ist folgender Code in der
Boostrap-Datei notwendig:

``` php
use VENDOR\namespace\of\filter\XssProtectionInputFilter;
InputFilterChain::getInstance()->prependFilter(new XssProtectionInputFilter());
```

Das APF bietet mit dem Templating-Mechanismus über die
*&lt;\*:importdesign /&gt;*-Tags bereits ausreichenden Schutz für
XSS-Injections per URL-Parameter. Dies greift jedoch nicht für die
direkte Verwendung von GET-Parametern in eigenen Tags und Controllern.
Sollen auch diese gesäubert werden, kann der Code wie folgt angepasst
werden:

``` php
namespace VENDOR\namespace\of\filter;

class XssProtectionInputFilter implements ChainedContentFilter {

   private $charset;

   public function __construct() {
      $this->charset = Registry::retrieve('APF\core', 'Charset');
   }

   public function filter(FilterChain &$chain, $input = null) {
      foreach ($_REQUEST as $key => $value) {
         if (is_array($value)) {
            $cleanValue = array();
            foreach ($value as $innerKey => $innerValue) {
               $cleanValue[$innerKey] = $this->replacePotentialXssContent($innerValue);
            }
         } else {
            $cleanValue = $this->replacePotentialXssContent($value);
         }
         $_REQUEST[$key] = $cleanValue;
      }
      $chain->filter($input);
   }

   private function replacePotentialXssContent($input) {
      return strip_tags(html_entity_decode($input, ENT_QUOTES, $this->charset));
   }

}
```

Folgende Alternative kümmert sich um sowohl **POST** als auch **GET**
Daten:

``` php
namespace VENDOR\namespace\of\filter;

class XssProtectionInputFilter implements ChainedContentFilter {

   private $charset;

   public function __construct() {
      $this->charset = Registry::retrieve('APF\core', 'Charset');
   }

   public function filter(FilterChain &$chain, $input = null) {

      if (count($_POST) > 0) {
         foreach ($_POST as $key => $value) {
            if (is_array($value)) {
               $cleanValue = array();
               foreach ($value as $innerKey => $innerValue) {
                  $cleanValue[$innerKey] = $this->replacePotentialXssContent($innerValue);
               }
            } else {
               $cleanValue = $this->replacePotentialXssContent($value);
            }
            $_POST[$key] = $cleanValue;
            $_REQUEST[$key] = $cleanValue;
         }
      }

      if (count($_GET) > 0) {
         foreach ($_GET as $key => $value) {
            if (is_array($value)) {
               $cleanValue = array();
               foreach ($value as $innerKey => $innerValue) {
                  $cleanValue[$innerKey] = $this->replacePotentialXssContent($innerValue);
               }
            } else {
               $cleanValue = $this->replacePotentialXssContent($value);
            }
            $_GET[$key] = $cleanValue;
            $_REQUEST[$key] = $cleanValue;
         }
      }

      $chain->filter($input);
   }

   private function replacePotentialXssContent($input) {
      return strip_tags(html_entity_decode($input, ENT_QUOTES, $this->charset));
   }

}
```