**Wie in einer Frontcontroller-Action die Sprache der Applikation
geändert werden kann, zeigt folgendes Beispiel.**

Die Sprache kann nach Einbau folgendes Codes über den URL-Parameter
'language' festgesetzt werden. Die aktuelle Sprache wird mithilfe des
SessionManagers gespeichert, und wird geladen falls kein URL-Parameter
gefunden wird. Sollte beides nicht vorhanden sein, wird 'de' als
Standartsprache festgelegt.

*Hinweis: Beim Festsetzen der Sprache über URL-Parameter sollte geprüft
werden, ob die Sprache überhaupt vorhanden ist, andererseits würde es
nachfolgend zu fehlern in der Applikation kommen. Die Prüfung könnte
z.B. fest im Code oder über Datenbankobjekte erledigt werden. Dies würde
den Rahmen dieses HowTo's jedoch sprengen.*

Folgende run()-Methode muss in eine konfigurierte FC-Action, welche in
der Bootstrap registriert ist, eingebaut, und den eigenen Bedürfnissen
angepasst werden:

``` php
public function run() {

   // load SessionManager
   $session = new Session('VENDOR\mytools\languagechoose'); // <<< set your favourite namespace here

   // set default language to 'de'
   $lang = 'de';

//<-- try loading language of URL-parameter
   $urlLang = RequestHandler::getValue('language');
   if ($urlLang !== null) {
      $lang = $urlLang;
   } //-->

//<-- Otherwise try loading already chosen language
   else if (($tmpLang = $session->load('language')) !== false) { //H int: if you are using 1.12 package or higher,
      $lang = $tmpLang; // you need to check for '!== null' instead of  '!== false' !
   }
//-->

   unset($urlLang);
   unset($tmpLang);

//<-- Save chosen language in session and set it in the application
   $session->save('language', $lang);

   $actions = & $this->getFrontController()->getActions();
   foreach ($actions as $hash => $DUMMY) {
      $actions[$hash]->setLanguage($lang);
   }
   $this->getFrontController()->setLanguage($lang);
//-->

}
```

**Zur Erklärung:** In einer Action hat man mit der Methode
$this-&gt;getFrontController() eine Referenz auf den Front-Controller.
Mittels

``` php
$actions = &$this->getFrontController()->getActions();
```

kann man sich den Stack der aktuellen Actions geben lassen und diese per

``` php
foreach($action as $hash => $DUMMY){
   $action[$hash]->setLanguage($lang);
}
```

mit der gewünschten Sprache versorgen. Der Front-Controller selber wird
über

``` php
$this->getFrontController()->setLanguage($lang);
```

konfiguriert.

`Eine weitere Implementierung findet sich im `[`Forum`](http://forum.adventure-php-framework.org/de/viewtopic.php?f=11&t=558)`.`