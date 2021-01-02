## Einleitung

In diesem HowTo werde ich eine mögliche Vorgehensweise bei der
Verwendung von jQuery und AJAX mit dem APF vorstellen. Es ist
möglicherweise nicht die 100%ige Methode für alle Anwendungsfälle und
Bedürfnisse, sollte für die meisten Anwendungsfälle aber funktionieren.
Die URLs in diesem Beispiel gehen von DEAKTIVIERTEM URL-Rewriting aus,
bei aktivem Rewriting muss das Format angepasst werden. (Siehe hierzu
die Anmerkung am Schluss)

## Vorraussetzungen

Ihr benötigt natürlich ein Template in welchem ihr jQuery eingebunden
habt. Weiterhin müsst ihr natürlich eure eigenen Javascript-Dateien
(jQuery Plugins und andere) einbinden. (Tipp: Sollte es sich um sehr
viele Dateien handeln, lohnt ein Blick auf die Extension
[JsCssPackager](/Javascript-_und_Css-Paketmanager "wikilink"), mit der
man nurnoch eine Datei einbinden braucht.) Die hier beschriebene
Möglichkeit setzt außerdem die Verwendung des Frontcontrollers vorraus,
da wir mit FC-Actions arbeiten werden.

## Die Theorie

Alle AJAX-Requests laufen über eine einzige dafür vorgesehene
Frontcontroller-Action, welche mithilfe des Command-Pattern die weitere
Verarbeitung einleitet. Hierzu wird ein Parameter (z.b. "cmd" genannt)
verwendet, der den Name des Commands übergibt der geladen werden soll.
Die FC-action kapselt dann die weiteren Parameter in beispielsweise
einem eigenen Data-Object (ich mach das in diesem Beispiel der
einfachheit als assoziatives Array) und gibt dieses an den Command
weiter. Der Command verarbeitet die Daten und gibt die gewünschte
Antwort aus, welche dann in javascript verarbeitet werden kann.

## Die Praxis

Genug Theorie, machen wir uns an die Praxis. Das URL-Layout soll wie
folgt aussehen:
index.php?ajaxapi-action:AjaxAPI=cmd:Hello\|param1:world\|foo:bar Wobei
beliebig viele Parameter angegeben werden können.

Die Ordnerstruktur wähle ich wie folgt, ihr könnt das natürlich anpassen
wenn ihr wollt:

``` php
/VENDOR
----/config
--------/ajaxapi
----/ajaxapi
--------/commands
```

Im Ordner "ajaxapi" erstellen wir eine FC-Action:

AjaxAPIAction.php

``` php
<?php
namespace VENDOR\ajaxapi;

use APF\core\frontcontroller\AbstractFrontcontrollerAction;
use APF\core\service\APFService;
use APF\tools\http\HeaderManager;

class AjaxAPIAction extends AbstractFrontcontrollerAction {

    public function run()
    {
        // first we send some cache headers so the browser won't cache any request of this action!
        HeaderManager::send( 'Expires: Mon, 26 Jul 1997 05:00:00 GMT' );
        HeaderManager::send( 'Last-Modified: ' . gmdate( 'D, d M Y H:i:s' ) . 'GMT' );
        HeaderManager::send( 'Cache-Control: no-cache, must-revalidate' );
        HeaderManager::send( 'Pragma: no-cache' );

        // we load the command-parameter and throw an exception if it was not found
        $cmd = $this->getInput()->getAttribute( 'cmd' );
        if($cmd === null) {
            throw new InvalidArgumentException( 'Command-parameter not found!' );
        }

        // secure the command input with whitelist in order to prevent any manipulation of the path the command will be searched in.
        $cmd = preg_replace( '/[^A-Za-z0-9_\-]+/', '', $cmd );

        // we load all parameters of the action into an array und remove the command-parameter
        $parameters = $this->getInput()->getAttributes();
        unset( $parameters['cmd'] );

        // get and execute command
        try {
            $command = $this->getServiceObject( 'VENDOR\ajaxapi\commands\\' . $cmd . 'AjaxAPICommand', APFService::SERVICE_TYPE_NORMAL, '' );
            $command->init( $parameters );
        }
        catch( IncludeException $e ) {
            throw new InvalidArgumentException( 'Command not found!' );
        }
        $command->execute();
        exit(0); // we exit because we don't wont the normal website to be rendered!
    }

}
```

Die Action wird jetzt im Ordner "/ajaxapi/commands" nach einer Klasse
mit folgendem Namensmuster suchen und eine Instanz davon erstellen:
{Cmd-parameter}AjaxAPICommand, also beispielsweise:
"HelloAjaxAPICommand". Als $initParam übergibt sie das Array mit den
Parametern. Dann ruft es die Methode "execute()" dieser Instanz auf.

Jetzt brauchen wir noch die Input Klasse für die Action, auch wenn diese
hier nichts besonderes enthält: /ajaxapi/AjaxAPIInput.php

``` php
<?php
namespace VENDOR\ajaxapi;

use APF\core\frontcontroller\FrontcontrollerInput;

class AjaxAPIInput extends FrontcontrollerInput {
    function __construct(){}
}
```

Und noch eine Konfiguration für die Action. Diese legen wir an unter:

/config/ajaxapi/{CONTEXT}/{ENVIRONMENT}_actionconfig.ini

``` ini
[AjaxAPI]
FC.ActionClass = "apps\ajaxapi\AjaxAPIAction"
FC.InputClass = "apps\ajaxapi\AjaxAPIInput"
```

Nachdem wir jetzt alle Vorraussetzungen erfüllt haben, widmen wir uns
nun den Commands. Als erstes definieren wir ein Interface welches die
Commands implementieren müssen, so sieht meines aus:

/ajaxapi/AjaxAPICommand.php

``` php
<?php
namespace VENDOR\ajaxapi;

/**
 * Interface for commands of AjaxAPI.
 * @author Ralf Schubert <ralf.schubert[a*t]the-screeze.de>
 */
interface AjaxAPICommand {

    /**
     * This function has to handle the command and generate the output.
     */
    function execute();

    /**
     * This function has to retrieve the Parameters.
     */
    function init($initParam);

}
```

Als nächstes legen wir uns den Command für unser Beispiel an, der soll
einfach mal irgendwas mit den Parametern machen:

/ajaxapi/commands/HelloAjaxAPICommand.php

``` php
<?php
namespace VENDOR\ajaxapi\commands;

use VENDOR\ajaxapi\AjaxAPICommand;
use APF\core\pagecontroller\APFObject;

class HelloAjaxAPICommand extends APFObject implements AjaxAPICommand {

    protected $__Parameter = null;

    public function init($initParam){
        $this->__Parameter = $initParam;
    }

    public function execute(){

        //we do anything with the parameters and echo any output...
        if(isset($this->__Parameter['foo']) && $this->__Parameter['foo'] === 'bar'){
            echo 'Hello '.html_entity_decode($this->__Parameter['param1']). '!<br />';
        }
        else {
            echo 'Hello<br />';
        }
        // we could also add some javascript to the output, e.g. use an function and give it some data from PHP:
        $anystring = 'blubb';
        echo '<script type="text"javascript">anyjsfunctionwewanttoexecute("'.$anystring.'");</script>';

    }
}
```

**WICHTIG: Die Commands müssen von APFObject ableiten, oder von einer
Klasse die dies tut.**

Ihr könnt auch JSON zurückgeben, hierzu legt ihr am einfachsten ein
Array an, welches ihr mit json_encode() serialisiert und per echo
ausgebt.

Die Arbeit mit dem APF ist nun eigentlich erstmal getan, jetzt brauchen
wir nurnoch mit jQuerys AJAX Funktionen die Daten abzurufen,
beispielsweise so:

``` javascript
$.get(
  'index.php?ajaxapi-action:AjaxAPI=cmd:Hello|param1:world|foo:bar',
  function(data) {
     $('.result').html(data);
     alert('Load was performed.');
  }
);
```

Tipp: Es bietet sich an eine Javascriptfunktion zu schreiben, welche den
Aufruf von jQuerys ajax(),get(),post(), getJSON() oder welche funktion
erwünscht ist kapselt und die URL zusammensetzt. Sollte URL-Rewriting
an/abgeschalten werden, habt ihr nurnoch eine Stelle code an der ihr was
ändern müsstet, nämlich die Funktion, die die URL zusammensetzt.