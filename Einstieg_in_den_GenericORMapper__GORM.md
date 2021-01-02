## Zielsetzung

Im vorliegenden Tutorial soll anhand eines einfachen Beispiels die
Verwendung des APF-eigenen
[GenericORMappers](http://adventure-php-framework.org/Seite/063-Generischer-OR-Mapper)
verdeutlicht werden.

Es wird eine Beispielanwendung erzeugt, die es ermöglicht, zunächst
Autoren zu erfassen. In einer weiteren Maske stehen diese Autoren zur
Verfügung und es wird das Hinzufügen eines Buchtitels ermöglicht. Des
weiteren soll die Möglichkeit bestehen, die bereits erfassten Autoren
sowie die Bücher in Listenform darzustellen. Auch eine Darstellung der
Zuordnung Autor =&gt; Bücher soll möglich sein. Weiterhin soll eine
Löschfunktion das Löschen eines einzelnen Buches sowie das Löschen eines
Autors und aller ihm zugeordneten Bücher möglich sein.

Funktionsumfang:

-   Hinzufügen eines Autors
-   Anzeigen aller erfassten Autoren
-   Hinzufügen eines Buches mit der Auswahlmöglichkeit eines zuvor
    erfassten Autors
-   Anzeigen aller Bücher
-   Anzeigen aller Bücher, geordnet nach dem Autor
-   Löschen eines Buches
-   Löschen eines Autors und aller ihm zugeordneten Bücher

## Voraussetzungen

Das vorliegende Tutorial wurde unter Verwendung des APF 2.0 erstellt.

Der Anwender sollte Grundkenntnisse im Umgang mit dem APF
(Ordnerstruktur, Namespaces) haben. Da dass Tutorial einfach gehalten
ist, sollte es jedoch auch für APF-Einsteiger nachvollziehbar sein.

### Eingesetzte APF-Komponenten

[Page-Controller](http://adventure-php-framework.org/Seite/098-Page-Controller)

[Front-Controller](http://adventure-php-framework.org/Seite/012-Frontcontroller)

[Templates](http://adventure-php-framework.org/Seite/047-Templates)

[Document-Controller](http://adventure-php-framework.org/Seite/006-Controller)

[Konfiguration](http://adventure-php-framework.org/Seite/134-Konfiguration)

[Standard-TagLibs](http://adventure-php-framework.org/Seite/046-Standard-TagLibs)

[Formulare](http://adventure-php-framework.org/Seite/113-Formulare)

[Verwendung von
Formulare](http://adventure-php-framework.org/Seite/114-Verwendung-von-Formularen)

[HeaderManager](http://adventure-php-framework.org/Seite/080-HeaderManager)

[Generischer-OR-Mapper](http://adventure-php-framework.org/Seite/063-Generischer-OR-Mapper)

## Verzeichnis- und Dateistruktur

``` php
/
index.php
-/css
--default.css
-/APF
--/books
---/pres
----/controller
------AddAuthorController.php
------AddBookController.php
------AuthorBooksListController.php
------AuthorsListController.php
------BooksListController.php
------DeleteAuthorController.php
------DeleteBookController.php
----/model
----/view
------author.html
------authorbookslist.html
------authorlist.html
------book.html
------booklist.html
------deleteauthor.html
------deletebook.html
------main.html
------start.html
--/config
---/core
----/database
-----/books
-------DEFAULT_connections.ini
----/modules
-----/gorm
------/books
--------DEFAULT_books_objects.ini
--------DEFAULT_books_relations.ini
```

## Vorbereitung Datenbank

### DEFAULT_connections.ini

*/APF/config/core/database/books/DEFAULT_connections.ini*

``` ini
[books]
DB.Host = "localhost"
DB.User = "user"
DB.Pass = "pwd"
DB.Name = "books"
DB.Type = "APF\core\database\MySQLxHandler"
DB.Charset = "utf8"
DB.Collation = "utf8_general_ci"
```

### DEFAULT_books_objects.ini

*/APF/config/modules/gorm/books/DEFAULT_books_objects.ini*

``` ini
[Authors]
Author = "VARCHAR(30)"

[Books]
Title = "VARCHAR(30)"
Price = "DECIMAL(6,2)"
```

### DEFAULT_books_relations.ini

*/APF/config/modules/gorm/books/DEFAULT_books_relations.ini*

``` ini
[Author2Book]
Type = "COMPOSITION"
SourceObject = "Authors"
TargetObject = "Books"
```

### Automatisiertes GORM Datenbank-Setup

*/APF/modules/genericormapper/data/tools/setup.php*

``` php
use APF\core\configuration\ConfigurationManager;
use APF\core\configuration\provider\ini\IniConfigurationProvider;
use APF\modules\genericormapper\data\tools\GenericORMapperManagementTool;

require('../../../../../APF/core/bootstrap.php');
$langProv = new IniConfigurationProvider();
$langProv->setOmitContext(false);
ConfigurationManager::registerProvider('ini', $langProv);

$setup = new GenericORMapperManagementTool();
$setup->setContext('books');

$setup->addDomainObjectsConfiguration('APF\modules\gorm', 'books');
$setup->addMappingConfiguration('APF\modules\gorm', 'books');
$setup->setConnectionName('books');

$setup->run();
```

### Ergebnis des GORM Datenbank-Setup

Das Ausführen der *setup.php* erzeugt folgende Ausgabe:

``` sql
CREATE TABLE IF NOT EXISTS `ent_authors` (
  `AuthorsID` INT(5) UNSIGNED NOT NULL auto_increment,
  `Author` VARCHAR(30) character set utf8 NOT NULL default '',
  `CreationTimestamp` timestamp NOT NULL default CURRENT_TIMESTAMP,
  `ModificationTimestamp` timestamp NOT NULL default '0000-00-00 00:00:00',
  PRIMARY KEY (`AuthorsID`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

CREATE TABLE IF NOT EXISTS `ent_books` (
  `BooksID` INT(5) UNSIGNED NOT NULL auto_increment,
  `Title` VARCHAR(30) character set utf8 NOT NULL default '',
  `Price` DECIMAL(6,2),
  `CreationTimestamp` timestamp NOT NULL default CURRENT_TIMESTAMP,
  `ModificationTimestamp` timestamp NOT NULL default '0000-00-00 00:00:00',
  PRIMARY KEY (`BooksID`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

CREATE TABLE IF NOT EXISTS `cmp_author2book` (
  `Source_AuthorsID` INT(5) UNSIGNED NOT NULL default '0',
  `Target_BooksID` INT(5) UNSIGNED NOT NULL default '0',
  KEY `JOIN` (`Source_AuthorsID`, `Target_BooksID`),
  KEY `REVERSEJOIN` (`Target_BooksID`, `Source_AuthorsID`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
```

### Inhalt der Datenbank

In der entsprechenden Datenbank sollten automatisch folgende Tabellen
erzeugt worden sein:

-   *cmp_author2book*
-   *ent_authors*
-   *ent_books*

Hiermit sind die Vorbereitungen zum Einsatz des GORM abgeschlossen und
es kann die Umsetzung des Projekts beginnen.

## Umsetzung

### Bootstrap erstellen

Die zentrale Datei der Anwendung ist die *index.php*, welche direkt in
*/* liegt.

``` php
use APF\core\frontcontroller\Frontcontroller;
use APF\core\singleton\Singleton;

require('./APF/core/bootstrap.php');

/* @var $fC Frontcontroller */
$fC = & Singleton::getInstance('APF\core\frontcontroller\Frontcontroller');
$fC->setContext('books');

echo $fC->start('APF\books\pres\view', 'main');
```

### Hauptseite

Die Hauptseite (*/APF/books/pres/view/main.html*) ist das zentrale
Element der Anwendung. Über sie werden später die einzelnen Unterseiten
angesteuert.

``` html4strict
<!DOCTYPE HTML>
<html>
  <head>
    <title>Books</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <link rel="stylesheet" type="text/css" href="./css/default.css" />
  </head>
  <body>

    <h1>Navigation</h1>
    <ul>
    <li><a href="?page=start" title="Home">Home</a></li>
    <li><a href="?page=author" title="Add author">Add author</a></li>
    <li><a href="?page=authorlist" title="List authors">List authors</a></li>
    <li><a href="?page=book" title="Add book">Add book</a></li>
    <li><a href="?page=booklist" title="List books">List books</a></li>
    <li><a href="?page=authorbookslist" title="List authors and books">List authors and books</a></li>
    <li><a href="?page=deletebook" title="Delete a book">Delete a book</a></li>
    <li><a href="?page=deleteauthor" title="Delete an author and his books">Delete an author and his books</a></li>
    </ul>

    <core:importdesign namespace="APF\books\pres\view" template="[page=start]" incparam="page"/>

  </body>
</html>
```

Dazu noch eine Startseite */APF/books/pres/view/start.html*:

``` html4strict
<h2>Welcome to GenericORMapper-Demopage</h2>
<p>This example shows the use of the APF GenericORMapper.</p>
<ol>
    <li>First, you should create a new author.</li>
    <li>After that, you are redirected to an list of existing authors.</li>
    <li>Finally you can create a new book and assign an author.</li>
    <li>At last you are redirected to an list of existing books.</li>
    <li>Further a book can be deleted or an author and all assigned books.</li>
</ol>
```

#### CSS-Datei

Um bei fehlerhaften Eingaben (z.B. Autorenname zu kurz) die
entsprechenden Felder farblich zu markieren wird noch unter
*/css/default.css* eine entsprechende CSS-Definition abgelegt.

``` css
.apf-form-error{
    border: 2px solid red;
}
```

### View zur Erfassung eines Autors

Unter */APF/books/pres/views/author.html* wird das Template zur
Erfassung eines Autors abgelegt. In diesem kann wie im folgenden
Beispiel umgesetzt auch eine Validierung stattfinden (Textlänge des
Autorennamens von mindestens 3 und maximal 30 Zeichen, sowie eine
Filterung der Eingabe hinsichtlich bestimmter Zeichen.

``` html4strict
<@controller
   class="APF\books\pres\controller\AddAuthorController"
@>
<core:addtaglib class="APF\tools\form\taglib\HtmlFormTag" prefix="html" name="form" />
<h2>Add a new author</h2>
<html:form name="AddAuthor">
   <p>
      <label for="Author">Name:</label>
      <form:text name="Author" id="Author" minlength="3" maxlength="30"/>
   </p>
   <p>
      <form:reset name="Reset" value="Clear form"/>
      <form:button name="SubmitAuthor" value="Add Author"/>
   </p>

   <form:addvalidator class="APF\tools\form\validator\TextLengthValidator" control="Author" button="SubmitAuthor"/>
   <form:addfilter class="APF\tools\form\validator\SpecialCharacterFilter" control="Author" button="SubmitAuthor"/>

</html:form>
```

#### Controller zur Erfassung eines Autors

Der entsprechende Controller wird unter
*/APF/books/pres/controller/AddAuthorController.php* abgelegt. Nach dem
Speichern des neuen Autors erfolge eine automatische Weiterleitung zur
Auflistung aller vorhandenen Autoren mittels dem
[HeaderManager](http://adventure-php-framework.org/Seite/080-HeaderManager)

``` php
namespace APF\books\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;
use APF\modules\genericormapper\data\GenericDomainObject;
use APF\modules\genericormapper\data\GenericORMapperFactory;
use APF\modules\genericormapper\data\GenericORRelationMapper;
use APF\tools\http\HeaderManager;

class AddAuthorController extends BaseDocumentController {

   public function  transformContent() {

      $form = & $this->getForm('AddAuthor');

      if ($form->isSent() && $form->isValid()) {

         $Author = new GenericDomainObject('Authors');
         $Author->setProperty('Author', $form->getFormElementByName('Author')->getAttribute('value'));

         $orm = $this->getMapper();
         $orm->saveObject($Author);

         // Redirect to the authors list
         HeaderManager::forward('?page=authorlist');

      } else {
         $form->transformOnPlace();
      }
   }

   /**
    * @return GenericORRelationMapper
    */
   private function getMapper() {
      /* @var $ormFact GenericORMapperFactory */
      $ormFact = & $this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
      return $ormFact->getGenericORMapper('APF\modules\gorm', 'books', 'books');
   }

}
```

### View zur Ausgabe aller Autoren

Mit diesem View werden alle vorhandenen Autoren aus der Datenbank
ausgelesen und in einer Tabelle dargestellt.

Der View zur Ausgabe aller Bücher ist identisch aufgebaut. Hier sind nur
entsprechende Änderungen hinsichtlich der Feldnamen sowie des
Controllers vorzunehmen.

``` html4strict
<@controller
   class="APF\books\pres\controller\AuthorsListController"
@>
<h2>Available authors</h2>
<table>
   <tr>
      <th>Author</th>
      <th>Date</th>
   </tr>
   <html:placeholder name="AuthorsList" />
</table>

<html:template name="AuthorsList">
   <tr>
      <td><template:placeholder name="AuthorName" /></td>
      <td><template:placeholder name="CreateDate" /></td>
   </tr>
</html:template>
```

#### Controller zur Ausgabe aller Autoren

Der Controller holt unter Verwendung des GORM sämtliche vorhandenen
Datensätze, weist sie den Platzhaltern im Template zu und gibt dieses
anschließend aus.

Der Controller zur Ausgabe aller Bücher ist identisch aufgebaut. Hier
sind lediglich die Bezeichnungen usw. anzupassen.

``` php
namespace APF\books\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;
use APF\modules\genericormapper\data\GenericCriterionObject;
use APF\modules\genericormapper\data\GenericORMapperFactory;
use APF\modules\genericormapper\data\GenericORRelationMapper;

class AuthorsListController extends BaseDocumentController {

   public function  transformContent() {

      $orm = $this->getMapper();

      $Crit = new GenericCriterionObject();
      $Crit->addPropertyIndicator('Author', '%');

      $AuthorsList = $orm->loadObjectListByCriterion('Authors', $Crit);

      $template = & $this->getTemplate('AuthorsList');

      $buffer = '';

      foreach ($AuthorsList as $Author) {
         $template->setPlaceHolder('AuthorName', $Author->getProperty('Author'));
         $template->setPlaceHolder('CreateDate', $Author->getProperty('CreationTimestamp'));
         $buffer .= $template->transformTemplate();
      }

      $this->setPlaceHolder('AuthorsList', $buffer);
   }

   /**
    * @return GenericORRelationMapper
    */
   private function getMapper() {
      /* @var $ormFact GenericORMapperFactory */
      $ormFact = & $this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
      return $ormFact->getGenericORMapper('APF\modules\gorm', 'books', 'books');
   }

}
```

### View zur Erfassung eines Buches

Hier wird die Möglichkeit geboten, ein neues Buch zu erfassen. Hierzu
erhält der Benutzer eine Auswahlmenü, in dem alle erfassten Autoren
erscheinen.

``` html4strict
<@controller
   class="APF\books\pres\controller\AddBookController"
@>
<core:addtaglib class="APF\tools\form\taglib\HtmlFormTag" prefix="html" name="form" />
<h2>Add a new book</h2>
<html:form name="AddBook">
   <p>
      <label for="Author">Author:</label>
      <form:select name="Author"/>
   </p>
   <p>
      <label for="Title">Title:</label>
      <form:text name="Title" id="Title"/>
   </p>
   <p>
      <label for="Price">Price:</label>
      <form:text name="Price" id="Price"/>
   </p>
   <p>
      <form:reset name="Reset" value="Clear forms"/>
      <form:button name="SubmitBook" value="Add book"/>
   </p>
</html:form>
```

#### Controller zur Erfassung eines Buches

##### Wichtiger Hinweis

Die Befüllung von *select*-Feldern muss **immer** stattfinden, da sonst
dem Controller die Möglichkeit genommen wird, den übermittelten Inhalt
zu überprüfen. Wird die Befüllung z.B. erst im *else*-Teil der
*if*-Bedingung durchgeführt, erhält der Controller keine Daten für das
Feld *AuthorID*. Das wiederum hat zur Folge, dass ein neuer, namenloser
Autor angelegt wird und mit diesem das neu erfasste Buch verknüpft wird.

``` php
namespace APF\books\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;
use APF\modules\genericormapper\data\GenericCriterionObject;
use APF\modules\genericormapper\data\GenericDomainObject;
use APF\modules\genericormapper\data\GenericORMapperFactory;
use APF\modules\genericormapper\data\GenericORRelationMapper;
use APF\tools\form\taglib\SelectBoxTag;
use APF\tools\http\HeaderManager;

class AddBookController extends BaseDocumentController {

   public function  transformContent() {

      $form = & $this->getForm('AddBook');

      $orm = $this->getMapper();

      $Crit = new GenericCriterionObject();
      $Crit->addPropertyIndicator('Author', '%');

      $AuthorsList = $orm->loadObjectListByCriterion('Authors', $Crit);

      /* @var $selectOption SelectBoxTag */
      $selectOption = & $form->getFormElementByName('Author');

      foreach ($AuthorsList as $Author) {
         $selectOption->addOption($Author->getProperty('Author'), $Author->getProperty('AuthorsID'));
      }

      if ($form->isSent() && $form->isValid()) {

         $Book = new GenericDomainObject('Books');
         $Book->setProperty('Title', $form->getFormElementByName('Title')->getAttribute('value'));
         $Book->setProperty('Price', $form->getFormElementByName('Price')->getAttribute('value'));

         $Author = new GenericDomainObject('Authors');
         $Author->setObjectId($selectOption->getSelectedOption()->getValue());

         $Book->addRelatedObject('Author2Book', $Author);

         $orm = $this->getMapper();

         $orm->saveObject($Book);

         HeaderManager::forward('?page=booklist');

      } else {
         $form->transformOnPlace();
      }
   }

   /**
    * @return GenericORRelationMapper
    */
   private function getMapper() {
      /* @var $ormFact GenericORMapperFactory */
      $ormFact = & $this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
      return $ormFact->getGenericORMapper('APF\modules\gorm', 'books', 'books');
   }

}
```

### View zur Ausgabe einer Auflistung der Autoren und ihrer Bücher

Die Datei */APF/books/pres/view/authorbookslist.html* enthält das
Grundgerüst, welches durch den Controller befüllt wird.

``` html4strict
<@controller
   class="APF\books\pres\controller\AuthorBooksListController"
@>
<h2>List of all authors and their books</h2>
<table>
   <tr>
      <th>Author</th>
      <th>Title</th>
      <th>Price</th>
      <th>Date</th>
   </tr>
   <html:placeholder name="DisplayAll" />
</table>

<html:template name="DisplayAll">
   <tr>
      <td><template:placeholder name="AuthorName" /></td>
      <td><template:placeholder name="Title" /></td>
      <td><template:placeholder name="Price" /></td>
      <td><template:placeholder name="CreateDate" /></td>
   </tr>
</html:template>
```

#### Controller zur Ausgabe der Bücherliste nach Autoren

Der Controller
*/APF/books/pres/controller/AuthorBooksListController.php*:

``` php
namespace APF\books\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;
use APF\modules\genericormapper\data\GenericCriterionObject;
use APF\modules\genericormapper\data\GenericORMapperFactory;
use APF\modules\genericormapper\data\GenericORRelationMapper;

class AuthorBooksListController extends BaseDocumentController {

   public function  transformContent() {

      $orm = $this->getMapper();

      $Crit = new GenericCriterionObject();
      $Crit->addPropertyIndicator('Author', '%');
      $Crit->addOrderIndicator('Author');

      $AuthorsList = $orm->loadObjectListByCriterion('Authors', $Crit);

      $template = & $this->getTemplate('DisplayAll');

      $buffer = '';

      foreach ($AuthorsList as $Author) {

         $BooksList = $orm->loadRelatedObjects($Author, 'Author2Book');

         foreach ($BooksList as $Book) {

            $template->setPlaceHolder('AuthorName', $Author->getProperty('Author'));

            $template->setPlaceHolder('Title', $Book->getProperty('Title'));
            $template->setPlaceHolder('Price', number_format($Book->getProperty('Price'), 2, ',', '.'));
            $template->setPlaceHolder('CreateDate', $Book->getProperty('CreationTimestamp'));
            $buffer .= $template->transformTemplate();

         }
      }

      $this->setPlaceHolder('DisplayAll', $buffer);
   }

   /**
    * @return GenericORRelationMapper
    */
   private function getMapper() {
      /* @var $ormFact GenericORMapperFactory */
      $ormFact = & $this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
      return $ormFact->getGenericORMapper('APF\modules\gorm', 'books', 'books');
   }

}
```

### View zum Löschen eines Buches

``` html4strict
<@controller
   class="APF\books\pres\controller\DeleteBookController"
@>
<core:addtaglib class="APF\tools\form\taglib\HtmlFormTag" prefix="html" name="form" />
<h2>Delete a book</h2>
<html:form name="DeleteBook">
   <p>
      <label for="Book">Book:</label>
      <form:select id="Book" name="Books"/><br/>
   </p>
   <p>
      <form:button name="SubmitBook" value="Delete book"/>
   </p>
</html:form>
```

#### Controller zum Löschen eines Buches

Zum Löschen eines Objekts (in diesem Fall eines Buches), stellt das APF
die Methode *deleteObject()* zur Verfügung.

``` php
namespace APF\books\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;
use APF\modules\genericormapper\data\GenericCriterionObject;
use APF\modules\genericormapper\data\GenericDomainObject;
use APF\modules\genericormapper\data\GenericORMapperFactory;
use APF\modules\genericormapper\data\GenericORRelationMapper;
use APF\tools\form\taglib\SelectBoxTag;
use APF\tools\http\HeaderManager;

class DeleteBookController extends BaseDocumentController {

   public function  transformContent() {

      $form = & $this->getForm('DeleteBook');

      $orm = $this->getMapper();

      $Crit = new GenericCriterionObject();
      $Crit->addPropertyIndicator('Title', '%');

      $BooksList = $orm->loadObjectListByCriterion('Books', $Crit);

      /* @var $selectOption SelectBoxTag */
      $selectOption = & $form->getFormElementByName('Books');

      foreach ($BooksList as $Book) {
         $selectOption->addOption($Book->getProperty('Title'), $Book->getProperty('BooksID'));
      }

      if ($form->isSent() && $form->isValid()) {

         $Book = new GenericDomainObject('Books');
         $Book->setProperty('BooksID', $selectOption->getSelectedOption()->getValue());

         $orm = $this->getMapper();

         $orm->deleteObject($Book);

         HeaderManager::forward('?page=booklist');

      } else {
         $form->transformOnPlace();
      }
   }

   /**
    * @return GenericORRelationMapper
    */
   private function getMapper() {
      /* @var $ormFact GenericORMapperFactory */
      $ormFact = & $this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
      return $ormFact->getGenericORMapper('APF\modules\gorm', 'books', 'books');
   }

}
```

### View zum Löschen eines Autors und ihm zugeordneter Bücher

``` html4strict
<@controller
   class="APF\books\pres\controller\DeleteAuthorController"
@>
<h2>Delete an author and assigned books</h2>
<html:form name="DeleteAuthor">
   <p>
      <label for="Author">Author:</label>
      <form:select name="Author"/>
   </p>
   <p>
      <form:button name="SubmitBook" value="Delete author and assigned books"/>
   </p>
</html:form>
```

#### Controller zum Löschen eines Autors und ihm zugeordneter Bücher

``` php
namespace APF\books\pres\controller;

use APF\core\pagecontroller\BaseDocumentController;
use APF\modules\genericormapper\data\GenericCriterionObject;
use APF\modules\genericormapper\data\GenericDomainObject;
use APF\modules\genericormapper\data\GenericORMapperFactory;
use APF\modules\genericormapper\data\GenericORRelationMapper;
use APF\tools\form\taglib\SelectBoxTag;
use APF\tools\http\HeaderManager;

class DeleteAuthorController extends BaseDocumentController {

   public function  transformContent() {

      $form = & $this->getForm('DeleteAuthor');

      $orm = $this->getMapper();

      $Crit = new GenericCriterionObject();
      $Crit->addPropertyIndicator('Author', '%');

      $AuthorsList = $orm->loadObjectListByCriterion('Authors', $Crit);

      /* @var $selectOption SelectBoxTag */
      $selectOption = & $form->getFormElementByName('Author');

      foreach ($AuthorsList as $Author) {
         $selectOption->addOption($Author->getProperty('Author'), $Author->getProperty('AuthorsID'));
      }

      if ($form->isSent() && $form->isValid()) {

         $Author = new GenericDomainObject('Authors');
         $Author->setProperty('AuthorsID', $selectOption->getSelectedOption()->getValue());

         $BooksList = $orm->loadRelatedObjects($Author, 'Author2Book');

         $orm = $this->getMapper();
         foreach ($BooksList as $Book) {
            $orm->deleteObject($Book);
         }

         $orm = $this->getMapper();

         $orm->deleteObject($Author);

         HeaderManager::forward('?page=authorbookslist');

      } else {
         $form->transformOnPlace();
      }
   }

   /**
    * @return GenericORRelationMapper
    */
   private function getMapper() {
      /* @var $ormFact GenericORMapperFactory */
      $ormFact = & $this->getServiceObject('APF\modules\genericormapper\data\GenericORMapperFactory');
      return $ormFact->getGenericORMapper('APF\modules\gorm', 'books', 'books');
   }

}
```