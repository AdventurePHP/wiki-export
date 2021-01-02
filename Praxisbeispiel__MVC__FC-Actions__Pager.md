## Zielsetzung

Das Tutorial soll anhand eines einfachen Beispiels das Zusammenspiel
mehrere APF-Komponenten aufzeigen. Hierbei soll eine Umsetzung anhand
von MVC geschehen. Der Benutzer hat die Möglichkeit, Nachrichten in
einer Datenbank zu speichern. Diese werden beim Besuch der Seite
angezeigt.

Das Erfassen einer Nachricht, die bereits existiert, wird durch eine
entsprechende Meldung angezeigt und eine Speicherung unterbleibt.

Durch das Anklicken der Nachricht wird diese, durch das Auslösen einer
Frontcontroller-Action, gelöscht. Um die Übersichtlichkeit der
vorhandenen Nachrichten zu wahren, wird zudem der Pager eingesetzt.

## Voraussetzungen

Das vorliegende Tutorial wurde unter Verwendung des APF 1.14 erstellt.

Der Anwender sollte Grundkenntnisse im Umgang mit dem APF
(Ordnerstruktur, Namespaces) haben.

## Verzeichnis- und Dateistruktur

``` php
/
index.php
-/css
---default.css
-/EXAMPLE
--/messenger
---/fcactions
-----deleteMessageAction.php
-----deleteMessageInput.php
---/pres
----/controller
------messengerController.php
----/model
------messengerMapper.php
----/view
------main.html
----/validator
------DoubleEntry.php
--/EXAMPLE-config
---/actions
----/messenger
------DEFAULT_actionconfig.ini
--/APF-config
---/core
----/database
-----/messenger
-------DEFAULT_connections.ini
---/modules
----/pager
-----/messenger
-------DEFAULT_pager.ini
-------DEFAULT_load_entries_count.sql
-------DEFAULT_load_entry_ids.sql
```

## Vorbereitung Datenbank

### Anlegen der Datenbanktabelle

Die Datenbank *messenger* für das Anwendungsbeispiel beschränkt sich auf
eine einzelne Tabelle, in der die Datensätze gespeichert werden. Diese
muss im Vorfeld manuell angelegt werden.

``` sql
CREATE TABLE IF NOT EXISTS `messages` (
  `ID` int(11) NOT NULL AUTO_INCREMENT,
  `Message` varchar(100) NOT NULL,
  `ModificationTimestamp` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `message` (`message`)
) ENGINE=MyISAM  DEFAULT CHARSET=latin1 ;
```

### DEFAULT_connections.ini

Mit dieser Konfigurationsdatei wird dem APF der Zugriff auf die oben
angelegte Datenbank ermöglicht. Sie wird unter
*/APF-config/core/database/messenger/DEFAULT_connections.ini* abgelegt.

``` ini
[Messenger]
Host = "localhost"
User = "user"
Pass = "pwd"
Name = "messenger"
Type = "MySQLx"
Charset = "utf8"
Collation = "utf8_general_ci"
```

## Umsetzung

### Bootstrap erstellen

Die zentrale Datei der Anwendung ist die *index.php*, welche direkt in
*/* liegt.

``` php
use APF\core\frontcontroller\Frontcontroller;
use APF\core\loader\RootClassLoader;
use APF\core\loader\StandardClassLoader;
use APF\core\singleton\Singleton;

// Fehlermeldungen einschalten
ini_set('html_errors', 'on');

// Zeitzone setzen
date_default_timezone_set('Europe/Berlin');

$apfClassLoaderConfigurationRootPath = './APF-config';
require_once '../APF/core/bootstrap.php';

RootClassLoader::addLoader(new StandardClassLoader('EXAMPLE', './EXAMPLE', './EXAMPLE-config'));

/* @var $fC Frontcontroller */
$fC = &Singleton::getInstance('APF\core\frontcontroller\Frontcontroller');
$fC->setContext('messenger');

// Startseite anzeigen
echo $fC->start('EXAMPLE\messenger\pres\view', 'main');
```

### Ausgangstemplate erstellen

Das Ausgangstemplate */apps/messenger/pres/view/main.html* gibt ein
*input*-Feld aus und erlaubt es dem Benutzer dort eine Nachricht
einzutragen. Diese wird nach dem Absenden zunächst auf die [richtige
Länge
validiert](http://adventure-php-framework.org/Seite/113-Formulare#Chapter-4-1-1-TextLengthValidator)
und ggf. eine entsprechende Fehlermeldung ausgegeben. Zusätzlich erfolgt
mittels eines Filters die [Umwandlung von Sonderzeichen in
HTML-Entities](http://adventure-php-framework.org/Seite/113-Formulare#Chapter-5-1-3-OnlyHTMLEntitiesFilter)
sowie das Entfernen von [HTML- und
PHP-Code](http://adventure-php-framework.org/Seite/113-Formulare#Chapter-5-1-10-StripTagsFilter).

``` html4strict
<@controller
   class="EXAMPLE\messenger\pres\controller\MessengerController"
@>
<!DOCTYPE html>
<html>
<head>
   <title>Messages</title>
   <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
   <link rel="stylesheet" type="text/css" href="./css/default.css" />
</head>
<body>

<h1><a href="index_messenger.php">Messages</a></h1>

<html:form name="NewMessage">
   New Message <form:text name="NewMessage" minlength="6" maxlength="100"/>
   <form:button name="AddMessage" value="Add Message"/>
   <form:error><p class="info">Error: The message must consist of 6 to 100 characters.</p></form:error>
   <form:addvalidator
         class="APF\tools\form\validator\TextLengthValidator"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addfilter
         class="APF\tools\form\filter\OnlyHTMLEntitiesFilter"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addfilter
         class="APF\tools\form\filter\StripTagsFilter"
         control="NewMessage"
         button="AddMessage"
   />
</html:form>
<hr/>
</body>
</html>
```

Im weiteren Verlauf wird dieses Template sukzessiv ergänzt, so dass
unterhalb der Eingabemaske die bereits vorhandenen Nachrichten
dargestellt werden.

#### CSS-Datei

Unter */css/default.css* wird eine CSS-Datei angelegt, die sich um die
farbliche Gestaltung der Darstellung kümmert.

``` css
.apf-form-error{
    border: 2px solid red;
}

.info{
    background: red;
    color: white;
    padding: 0.5em;
}
```

#### Controller zum Speichern einer Nachricht

Der Controller
(*/EXAMPLE/messenger/pres/controller/messengerController.php*) prüft
zunächst, ob das abgesendete Formular valide ist. Trifft dies zu und die
neue Nachricht ist noch nicht vorhanden, so wird diese gespeichert.
Ansonsten wird das Eingabeformular erneut angezeigt.

``` php
namespace EXAMPLE\messenger\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;

class messengerController extends BaseDocumentController {

   public function  transformContent() {

      $form = &$this->getForm('NewMessage');

      if ($form->isSent() && $form->isValid()) {

         $message = $form->getFormElementByName('NewMessage')->getAttribute('value');

         $mM = &$this->getMapper();

         if ($mM->writeMessage($message)) {
            // ...im weiteren Verlauf erfolgen hier noch weitere Anweisungen
         } else {
            $form->transformOnPlace();
         }

      }

      $form->transformOnPlace();
   }

   private function &getMapper() {
      return $this->getServiceObject('EXAMPLE\messenger\data\MessengerMapper');
   }

}
```

#### Model zum Speichern der Daten

Mit dem nachfolgenden Model
(*/EXAMPLE/messenger/data/MessengerMapper.php*) ist die Prüfung möglich,
ob eine Nachricht bereits existiert sowie das Speichern einer neuen
Nachricht.

``` php
namespace EXAMPLE\messenger\data;

use APF\core\database\ConnectionManager;
use APF\core\pagecontroller\APFObject;

class messengerMapper extends APFObject {

   // Neue Nachricht eintragen
   public function writeMessage($Message) {

      // Datenbankverbindung
      $SQL = &$this->getConnection();

      // Existiert der Eintrag bereits?
      if ($this->checkMessage($Message)) {
         // Eintrag noch nicht vorhanden

         // Query formulieren
         $insert = 'INSERT INTO messages (`message`) VALUES (\'' . $SQL->escapeValue($Message) . '\')';

         // Daten schreiben
         $result = $SQL->executeTextStatement($insert);

         // ID holen
         $lastId = $SQL->getLastID();

         // Eintrag erfolgreich
         return true;
      }

      // Eintrag bereits vorhanden bzw. nicht erfolgreich
      return false;

   }

   // Prüft ob eine Nachricht bereits vorhanden ist
   public function checkMessage($Message) {

      // Datenbankverbindung
      $SQL = &$this->getConnection();

      // Query formulieren
      $select = 'SELECT `id` FROM `messages` WHERE `message` = \'' . $Message . '\'';

      // Query ausführen
      $result = $SQL->executeTextStatement($select);

      // Anzahl Datensätze holen
      $id = $SQL->getNumRows($result);

      if ($id) {
         // Eintrag existiert bereits
         return false;
      } else {
         // Eintrag exisitiert noch nicht
         return true;
      }
   }


   // Verbindung zur Datenbank herstellen
   protected function &getConnection() {
      /* @var $cM ConnectionManager */
      $cM = &$this->getServiceObject('APF\core\database\ConnectionManager');

      return $cM->getConnection('Messenger');
   }

}
```

### Validator zur Überprüfung existierender Nachrichten

Um dem Benutzer mitzuteilen, ob eine Nachricht bereits existiert, soll
ihm dies durch einen eigenen Validator mitgeteilt werden.

#### Anpassung des Ausgangstemplates

Die *EXAMPLE/messenger/pres/view/main.html* wird daher wie folgt
ergänzt:

``` html4strict
<@controller
   class="EXAMPLE\messenger\pres\controller\MessengerController"
@>
<!DOCTYPE html>
<html>
<head>
   <title>Messages</title>
   <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
   <link rel="stylesheet" type="text/css" href="./css/default.css" />
</head>
<body>

<h1><a href="index_messenger.php">Messages</a></h1>

<html:form name="NewMessage">
   New Message <form:text name="NewMessage" minlength="6" maxlength="100"/>
   <form:button name="AddMessage" value="Add Message"/>
   <form:error><p class="info">Error: The message must consist of 6 to 100 characters.</p></form:error>
   <form:listener name="DoublEntryListener" control="NewMessage" validator="DoubleEntry">
      <p class="info">Existing message!</p>
   </form:listener>
   <form:addvalidator
         class="APF\tools\form\validator\TextLengthValidator"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addfilter
         class="APF\tools\form\filter\OnlyHTMLEntitiesFilter"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addfilter
         class="APF\tools\form\filter\StripTagsFilter"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addvalidator
         class="EXAMPLE\messenger\pres\validator\DoubleEntry"
         control="NewMessage"
         button="AddMessage"
         type="special"
   />
</html:form>

<hr/>
</body>
</html>
```

#### DoubleEntry - Validator

Der Validator greift zur Überprüfung auf bereits existierende Einträge
auf die Methode *checkMessage()* der Klasse *MessengerMapper* (s.o.)
zurück.

``` php
namespace EXAMPLE\messenger\pres\validator\DoubleEntry;

use APF\tools\form\validator\TextFieldValidator;

class DoubleEntry extends TextFieldValidator {

   public function validate($Message) {

      $mM = &$this->getMapper();

      // Eintrag ist noch nicht vorhanden
      if ($mM->checkMessage($Message)) {
         return true;
      }

      // Eintrag ist bereits vorhanden
      return false;
   }

   private function &getMapper() {
      return $this->getServiceObject('EXAMPLE\messenger\data\MessengerMapper');
   }

}
```

### Ausgabe vorhandener Nachrichten

Als nächster Schritt sollen alle bereits vorhandenen Nachrichten
unterhalb des Eingabefeldes angezeigt werden. Um die Übersicht zu
wahren, kommt zudem der Pager zum Einsatz. Dieser soll maximal zehn
Nachrichten pro Seite anzeigen.

#### Anpassung des Templates

Zunächst erfolgt eine Anpassung des Templates.

``` html4strict
<@controller
   class="EXAMPLE\messenger\pres\controller\MessengerController"
@>
<!DOCTYPE html>
<html>
<head>
   <title>Messages</title>
   <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
   <link rel="stylesheet" type="text/css" href="./css/default.css" />
</head>
<body>

<h1><a href="index_messenger.php">Messages</a></h1>

<html:form name="NewMessage">
   New Message <form:text name="NewMessage" minlength="6" maxlength="100"/>
   <form:button name="AddMessage" value="Add Message"/>
   <form:error><p class="info">Error: The message must consist of 6 to 100 characters.</p></form:error>
   <form:listener name="DoublEntryListener" control="NewMessage" validator="DoubleEntry">
      <p class="info">Existing message!</p>
   </form:listener>
   <form:addvalidator
         class="APF\tools\form\validator\TextLengthValidator"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addfilter
         class="APF\tools\form\filter\OnlyHTMLEntitiesFilter"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addfilter
         class="APF\tools\form\filter\StripTagsFilter"
         control="NewMessage"
         button="AddMessage"
   />
   <form:addvalidator
         class="EXAMPLE\messenger\pres\validator\DoubleEntry"
         control="NewMessage"
         button="AddMessage"
         type="special"
   />
</html:form>

<hr/>

<h2>Messages</h2>
<p>(Click on message to delete)</p>
<table>
   <tr>
      <th>Message</th>
      <th>Timestamp</th>
   </tr>
   <html:placeholder name="list"/>
   <html:iterator name="MessageList">
      <iterator:item>
         <tr>
            <td><a href="<item:placeholder name="link" />"><item:placeholder name="Message" /></a></td>
            <td><item:placeholder name="ModificationTimestamp" /></td>
         </tr>
      </iterator:item>
   </html:iterator>
</table>
<p><html:placeholder name="Pager" /></p>

</body>
</html>
```

#### Konfiguration des Pagers

##### DEFAULT_pager.ini

Zunächst wird die Konfigurationsdatei
*/APF-config/config/modules/pager/messenger/DEFAULT_pager.ini* mit
nachfolgendem Inhalt angelegt.

``` ini
[messagePager]
DatabaseConnection = "Messenger"

EntriesPerPage = "10"

ParameterPageName = "pstart"
ParameterCountName = "pact"

StatementNamespace = "modules::pager"
CountStatement = "load_entries_count.sql"
CountStatement.Params = ""
EntriesStatement = "load_entry_ids.sql"
EntriesStatement.Params = ""

DesignNamespace = "modules::pager::pres::templates"
DesignTemplate = "pager_2"

CacheInSession = "false"

; seit 1.13. < 1.13 immer true
AllowDynamicEntriesPerPage = "true"
```

##### DEFAULT_load_entries_count.sql

*/APF-config/config/modules/pager/messenger/DEFAULT_load_entries_count.sql*

``` sql
SELECT COUNT(*) AS EntriesCount
FROM `messages`
```

##### DEFAULT_load_entry_ids.sql

*/APF-config/config/modules/pager/messenger/DEFAULT_load_entry_ids.sql*

``` sql
SELECT `messages`.`ID` AS DB_ID
FROM `messages`
ORDER BY `messages`.`ID` DESC
LIMIT [Start], [EntriesCount];
```

#### Anpassung des Controllers

Der Controller erfährt einige Änderungen, so dass er in Verbindung mit
dem Pager in der Lage ist, jeweils fünf vorhandene Nachrichten
auszugeben und man durch Blättern zu den weiteren Nachrichten gelangt.

Zudem erfolgt nach dem Speichern einer neuen Nachricht ein Reload der
Seite, so dass die neue Nachricht direkt in der List erscheint.

Die Methode *createMessageList()* stattet die vorhandenen Nachrichten
unter Verwendung des
[LinkGenerators](http://adventure-php-framework.org/Seite/138-Links) mit
einem Link aus, der es im nächsten Schritt ermöglicht, diese durch
Anklicken zu löschen.

``` php
namespace EXAMPLE\messenger\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;
use APF\modules\pager\biz\PagerManagerFabric;
use APF\tools\link\LinkGenerator;
use APF\tools\link\Url;

class messengerController extends BaseDocumentController {

   public function  transformContent() {

      $form = &$this->getForm('NewMessage');

      // Vorhandene Nachrichten auslesen und anzeigen
      $this->createMessageList();

      /* @var $pMF PagerManagerFabric */
      $pMF = &$this->getServiceObject('APF\modules\pager\biz\PagerManagerFabric');
      $pM = &$pMF->getPagerManager('messagePager');
      $this->setPlaceHolder('Pager', $pM->getPager());

      if ($form->isSent() && $form->isValid()) {

         // Nachricht holen
         $Message = $form->getFormElementByName('NewMessage')->getAttribute('value');

         // Mapper aufrufen
         $mM = &$this->getMapper();

         // Eintrag ist noch nicht vorhanden
         if ($mM->writeMessage($Message)) {
            self::getResponse()->forward('index.php');
         } // Eintrag ist bereits vorhanden
         else {
            // View ausgeben
            $form->transformOnPlace();
         }

      }

      $form->transformOnPlace();
   }

   // Nachrichtenliste erzeigen
   public function createMessageList() {

      // Kontakt zu Mapper herstellen
      $mM = &$this->getMapper();

      // Referenz auf den Interator
      $iterator = &$this->getIterator('MessageList');

      // Pagereinträge verwenden
      $messages = $mM->loadMessages();

      foreach ($messages as $message) {
         $message['link'] = LinkGenerator::generateActionUrl(
               Url::fromCurrent(),
               'actions',
               'deleteAction',
               array(
                     'MessageID' => $message['ID']
               ));
      }

      // Datencontainer befüllen
      $iterator->fillDataContainer($messages);

      // Iterator ausgeben
      $iterator->transformOnPlace();

   }

   // Datenbankverbindung herstellen
   private function &getMapper() {
      return $this->getServiceObject('EXAMPLE\messenger\data\MessengerMapper');
   }

}
```

#### Anpassung des Mappers

Das Model (*/EXAMPLE/messenger/data/MessengerMapper.php*) wird um die
Methoden *loadMessages()* sowie *loadArticleCommentByID()* ergänzt.

``` php
namespace EXAMPLE\messenger\data;

use APF\core\database\ConnectionManager;
use APF\core\pagecontroller\APFObject;
use APF\modules\pager\biz\PagerManagerFabric;

class messengerMapper extends APFObject {

   // Neue Nachricht eintragen
   public function writeMessage($Message) {

      // Datenbankverbindung
      $SQL = &$this->getConnection();

      // Existiert der Eintrag bereits?
      if ($this->checkMessage($Message)) {
         // Eintrag noch nicht vorhanden

         // Query formulieren
         $insert = 'INSERT INTO messages (`message`) VALUES (\'' . $SQL->escapeValue($Message) . '\')';

         // Daten schreiben
         $result = $SQL->executeTextStatement($insert);

         // ID holen
         $lastId = $SQL->getLastID();

         // Eintrag erfolgreich
         return true;
      }

      // Eintrag bereits vorhanden bzw. nicht erfolgreich
      return false;

   }

   // Prüft ob eine Nachricht bereits vorhanden ist
   public function checkMessage($Message) {

      // Datenbankverbindung
      $SQL = &$this->getConnection();

      // Query formulieren
      $select = 'SELECT `id` FROM `messages` WHERE `message` = \'' . $Message . '\'';

      // Query ausführen
      $result = $SQL->executeTextStatement($select);

      // Anzahl Datensätze holen
      $id = $SQL->getNumRows($result);

      if ($id) {
         // Eintrag existiert bereits
         return false;
      } else {
         // Eintrag exisitiert noch nicht
         return true;
      }
   }

   // Vorhandene Nachrichten auslesen
   public function loadMessages() {

      // Pager einbinden
      /* @var $pMF PagerManagerFabric */
      $pMF = &$this->getServiceObject('APF\modules\pager\biz\PagerManagerFabric');
      $pM = &$pMF->getPagerManager('messagePager');

      return $pM->loadEntriesByAppDataComponent($this, 'loadArticleCommentByID');

   }

   public function loadArticleCommentByID($id) {
      $SQL = &$this->getConnection();
      $select = 'SELECT * FROM `messages` WHERE `messages`.`ID` = \'' . $id . '\'';
      $result = $SQL->executeTextStatement($select);

      return $SQL->fetchData($result);
   }

   // Verbindung zur Datenbank herstellen
   protected function &getConnection() {
      /* @var $cM ConnectionManager */
      $cM = &$this->getServiceObject('APF\core\database\ConnectionManager');

      return $cM->getConnection('Messenger');
   }

}
```

### Nachricht löschen

Vorhandene Nachrichten sollen nun durch Anklicken des Titels gelöscht
werden. Die Vorarbeit wurde bereits unter Punkt 5.4. geleistet, indem
die Titel mit entsprechenden Links versehen wurden.

#### Konfigurationsdatei

*/apps/config/actions/messenger/DEFAULT_actionconfig.ini*

``` ini
[deleteAction]
ActionNamespace = "messenger::fcactions"
ActionFile = "deleteMessageAction"
ActionClass = "deleteMessageAction"
InputFile = "deleteMessageInput"
InputClass = "deleteMessageInput"
InputParams = ""
```

#### Action zum Löschen einer Nachricht

*/EXAMPLE/messenger/fcactions/DeleteMessageAction.php*

``` php
namespace EXAMPLE\fcaction;

use APF\core\frontcontroller\AbstractFrontcontrollerAction;

class deleteMessageAction extends AbstractFrontcontrollerAction {

   public function  run() {

      // Übergebene Id der Nachricht holen
      $id = $this->getInput()->getParameter('MessageID');

      // Kontakt zu Mapper herstellen
      $mM = &$this->getMapper();

      // Nachricht löschen
      $mM->deleteMessage($id);
   }

   private function &getMapper() {
      return $this->getServiceObject('EXAMPLE\messenger\data\MessengerMapper');
   }

}
```