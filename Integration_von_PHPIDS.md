## Einleitung

[PHPIDS](http://php-ids.org) ist ein in PHP geschriebenes *intrusion
detection system*. Es wird eingesetzt, um verdächtige Inhalte zu
dedektieren und die Applikation vor möglichen Einbrüchen zu schützen. In
diesem Tutorial geht es darum, PHPIDS in das APF zu integrieren.

## Idee

Das APF verfügt mit dem
[Filter](http://adventure-php-framework.org/Seite/087-Filter)-Konzept
über eine Möglichkeit, Benutzer-Eingaben zu einem sehr frühen Zeitpunkt
zu prüfen und entsprechend zu reagieren. Aus diesem Grund soll PHPIDS in
einem eigenen Filter eingesetzt werden, der eine Applikation effektiv
schützt.

## Umsetzung

Unter [php-ids.org](http://php-ids.org/faq/) wird eine einfache
Umsetzung für die Integration des PHPIDS gezeigt. Diese soll genutzt
werden, um die APF-Input-Filter zu erweitern.

### Grundgerüst

Das Grundgerüst eines APF-Input-Filters hat folgende Gestalt:

``` php
use APF\core\filter\ChainedContentFilter;

class IDSFilter implements ChainedContentFilter {

   public function filter(FilterChain &$chain, $input = null) {
      ...
   }

}
```

Da wir die bisherige Funktion der Filter weiter nutzen wollen um eine
transparente Einbindung des IDS-Filters zu gewährleisten, erweitern wir
diese.

### Fehlerbehandlung

Sofern der IDS-Monitor anschlägt, soll die Applikation nicht weiter
ausgeführt werden. Hierzu bedienen wir uns des [globalen
Exception-Handlings](http://adventure-php-framework.org/Seite/145-Exception-Behandlung),
das alle nicht in Applikationen abgefangenen Exceptions aufnimmt und mit
einer angepassten Fehler-Seite quittiert.

``` php
use APF\core\filter\ChainedContentFilter;
use \Exception;

require('IDS/Init.php');

class IDSFilter implements ChainedContentFilter {

   public function filter(FilterChain &$chain, $input = null) {

      // IDS-Monitor einbinden
      $request = array(
         'REQUEST' => $_REQUEST,
         'GET' => $_GET,
         'POST' => $_POST,
         'COOKIE' => $_COOKIE
      );
      $init = IDS_Init::init('IDS/Config/Config.ini');
      $ids = new IDS_Monitor($request,$init);
      $result = $ids->run();

      if(!$result->isEmpty()){
         throw new Exception('IDS monitor detected potential intrusion! Details: '.$result);
      }

      return $chain->filter($input);

   }

}
```

## Anwendung

Die Anwendung gestaltet sich sehr einfach: es muss in der
Bootstrap-Datei lediglich der entsprechene Filter angegeben werden:

``` php
use VENDOR\..\IDSFilter;

InputFilterChain::getInstance()
   ->prependFilter(new IDSFilter());
```

### Anpassung der Ausgabe

Das Ergebnis einer *intrusion detection* ist nun die Anzeige der
APF-Exception-Seite. Sollen die durch den Monitor gewonnenen
Informationen nicht direkt an den Benutzer geleitet werden, so bietet
sich an, den ExceptionHandler zu erweitern. Details dazu können der
Dokumentation im Kapitel [Implementierung eines
ExceptionHandler](http://adventure-php-framework.org/Seite/145-Exception-Behandlung#Chapter-3-Implementierung-eines-ExceptionHandler)
entnommen werden.