In CMS-ähnlichen Anwendungen ist es erforderlich, WYSIWYG-Editoren
(meist auf JavaScript basiert) einzubinden. Die folgende Klasse
beschreibt eine Formular-Taglib, mit der es möglich ist, den
[CKEditor](http://ckeditor.com) einzubinden. Der Tag besitzt ferner die
Möglichkeit, Context-abhängige Customer-Konfigurationen einzubinden:

``` php
namespace CMS\core\pres\taglib;

use APF\core\registry\Registry;
use APF\tools\form\taglib\AbstractFormControl;
use InvalidArgumentException;

/**
 * Implements a taglib, that integrates the FCK editor into APF forms.
 */
class CkEditorTag extends AbstractFormControl {

   public function transform() {

      $id = $this->getAttribute('id');
      if ($id === null) {
         throw new InvalidArgumentException('id attribute not set!', E_USER_ERROR);
      }

      $tagContent = (string) '';
      $tagContent .= '<script type="text/javascript" src="./ckeditor/ckeditor.js"></script>';
      $tagContent .= '<script type="text/javascript" src="./ckeditor/config.js"></script>';
      $tagContent .= '<textarea ';
      $tagContent .= $this->getAttributesAsString($this->getAttributes());
      $tagContent .= '>' . $this->getContent() . '</textarea>';
      $tagContent .= PHP_EOL . '<script type="text/javascript">';

      // config see http://docs.cksource.com/ckeditor_api/symbols/CKEDITOR.config.html
      $tagContent .= '  var editor = CKEDITOR.replace(
            \'' . $id . '\',
            {
               customConfig : \'' . $this->getCustomConfigPath() . '\',
               height: 600,
               filebrowserWindowWidth : \'1000\',
               filebrowserWindowHeight : \'700\',
               toolbar: [
                           [\'Source\',\'-\'],
                           [\'Bold\',\'Italic\',\'Underline\',\'Strike\',\'-\',\'Subscript\',\'Superscript\'],
                           [\'NumberedList\',\'BulletedList\',\'-\',\'Outdent\',\'Indent\',\'Blockquote\',\'CreateDiv\'],
                           [\'JustifyLeft\',\'JustifyCenter\',\'JustifyRight\',\'JustifyBlock\'],
                           [\'Link\',\'Unlink\',\'Anchor\'],
                           [\'Image\',\'Flash\',\'Table\',\'HorizontalRule\',\'SpecialChar\'],
                           \'/\',
                           [\'Styles\',\'Format\',\'Font\',\'FontSize\'],
                           [\'TextColor\',\'BGColor\'],
                           [\'ShowBlocks\'],

                           [\'Cut\',\'Copy\',\'Paste\',\'PasteText\',\'PasteFromWord\'],
                           [\'Undo\',\'Redo\',\'-\',\'Find\',\'Replace\',\'-\',\'SelectAll\',\'RemoveFormat\'],
                        ],
               startupOutlineBlocks : true,
               disableNativeSpellChecker : true,
               scayt_autoStartup : false
            }
         );';

      return $tagContent . PHP_EOL . '</script>';
   }

   private function getCustomConfigPath() {
      $configBasePath = Registry::retrieve('APF\core', 'URLBasePath');

      $url = parse_url($_SERVER['REQUEST_URI']);
      if (isset($url['path'])) {
         $configBasePath .= preg_replace('~/$~', '', $url['path']);
      }

      $env = Registry::retrieve('APF\core', 'Environment');
      $customer = Registry::retrieve('CMS\core', 'Customer');

      return $configBasePath . '/config/' . $env . '_config_' . $customer . '.js';
   }

   /**
    * Removes the duplicate slashes to avoid issues with saving the article.
    *
    * @return string The content of the wysiwyg area.
    */
   public function getValue() {
      if (ini_get('magic_quotes_gpc') === '1') {
         return stripcslashes($this->getContent());
      }

      return $this->getContent();
   }

   /**
    * Implements presetting for the ckeditor.
    */
   protected function presetValue() {
      $name = $this->getAttribute('name');
      $request = self::getRequest();
      if ($request->hasParameter($name)) {
         $this->setContent($request->getParameter($name));
      }
   }

}
```

Bei der Einbindung ist noch darauf zu achten, dass die relativen Pfade
der CK-Editor-Installation (im Beispiel "./ckeditor") angepasst werden.

Die Diskussion zu diesem HOWTO ist im Foren-Beitrag [Erfahrungen mit
WYSIWYG-Editor +
APF](http://forum.adventure-php-framework.org/de/viewtopic.php?f=7&t=496)
zu finden.