<translate>

### Einleitung

Aus Sicherheitsgründen sollte eine Applikation möglichst "unauffällig"
auf einen provozierten Fehler reagieren um nicht zu viel "Futter" für
weitere Angriffe zu geben.

Eine Möglichkeit ist, globale Fehler mit einem eigenen
[ErrorHandler](http://adventure-php-framework.org/Seite/101-Fehlerbehandlung#Chapter-3-Erweiterung)
abzufangen. Dieser nimmt den Fehler auf und leitet zu einem definierten
Punkt in der Applikation weiter. So wird der Fehler zur Auswertung und
Behebung zwar protokolliert, der Angreifer kann diese jedoch nicht
auswerten.

### Umsetzung

Die Umsetzung gestaltet sich dabei gemäß der oben verlinkten
Dokumentation sehr einfach:

``` php
use APF\core\errorhandler\DefaultErrorHandler;

<!--T:5-->
class LiveErrorHandler extends DefaultErrorHandler {

   <!--T:6-->
public function handleError($errorNumber, $errorMessage, $errorFile, $errorLine){

      <!--T:7-->
// fill attributes
      $this->errorNumber = $errorNumber;
      $this->errorMessage = $errorMessage;
      $this->errorFile = $errorFile;
      $this->errorLine = $errorLine;

      <!--T:8-->
// log error
      $this->logError();

      <!--T:9-->
// redirect to start page
      header('Location: /');
      exit(0);

   <!--T:10-->
}

<!--T:11-->
}
```

Grundsätzlich werden im APF-Code keine *trigger_error()*-Aufrufe
sondern konsequent Exceptions zur "Anzeige" von Fehlern genutzt. Aus
diesem Grund ist es empfehlenswert für den Live-Betrieb parallel zum
**LiveErrorHandler** (Errors können durch die PHP-API bedingt immer noch
auftreten) einen **LiveExceptionHandler** einzusetzen. Dieser kann wie
folgt aussehen:

``` php
use APF\core\exceptionhandler\DefaultExceptionHandler;

<!--T:14-->
class LiveExceptionHandler extends DefaultExceptionHandler{

   <!--T:15-->
public function handleException($exception){

      <!--T:16-->
// fill attributes
      $this->exceptionNumber = $exception->getCode();
      $this->exceptionMessage = $exception->getMessage();
      $this->exceptionFile = $exception->getFile();
      $this->exceptionLine = $exception->getLine();
      $this->exceptionTrace = $exception->getTrace();
      $this->exceptionType = get_class($exception);

      <!--T:17-->
// log exception
      $this->logException();

      <!--T:18-->
// redirect to start page
      header('Location: /');
      exit(0);

   <!--T:19-->
}

<!--T:20-->
}
```

Um die Behandlung von PHP-Fehlern und Exceptions zu vereinheitlichen
lässt sich der **LiveErrorHandler** auch wie folgt verändern:

``` php
use \ErrorException;
use APF\core\errorhandler\DefaultErrorHandler;

<!--T:23-->
class LiveErrorHandler extends DefaultErrorHandler {

   <!--T:24-->
public function handleError($errorNumber, $errorMessage, $errorFile, $errorLine){
      throw new ErrorException($errorMessage, $errorNumber, 1, $errorFile, $errorLine);
   }

<!--T:25-->
}
```

Sollte ein PHP-Fehler auftreten, wird dieser nun in eine Exception
verpackt und an den *LiveExceptionHandler* "weitergeleitet".

## Konfiguration

Um den *LiveErrorHandler* bzw. den *LiveExceptionHandler* zu
registrieren fügen Sie bitte folgende Zeilen zu ihrer Bootstrap-Datei
hinzu:

``` php
use APF\core\errorhandler\GlobalErrorHandler;
use VENDOR\..\LiveErrorHandler;
GlobalErrorHandler::registerErrorHandler(new LiveErrorHandler());

<!--T:30-->
use APF\core\exceptionhandler\GlobalExceptionHandler;
use VENDOR\..\LiveExceptionHandler;
GlobalExceptionHandler::registerExceptionHandler(new LiveExceptionHandler());
```

</translate> <languages />