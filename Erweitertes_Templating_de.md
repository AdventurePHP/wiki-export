Auf dieser Seite wird ein Feature-Vorschlag für erweitertes Templating
beschrieben. Die Erweiterung ist für den Versions-Zweig 2.x vorgesehen.

## Ausgangslage

1.  Platzhalter nur per *&lt;\*:placeholder name=""/&gt;* möglich.
    1.  Mehr Tipp-Arbeit als beispielsweise bei der Smarty-Syntax
    2.  Syntax-Highlighting in der IDE funktioniert nicht korrekt
2.  Das Setzen von Platzhaltern nimmt in Controllern einen deutlichen
    Anteil des Codes ein (gerade bei komplexen Objekten oder
    Objekt-Strukturen). Eine einfache Templating-Sprache zum Zugriff auf
    Objekte und deren eigenschaften würde das deutlich erleichtern.

## Paradigma

-   Trennung von Logik und Aussehen soll auch weiterhin forciert werden,
    daher weiterhin keine Kontrollstrukturen in Templates.

## Vorschlag

### Einführung neuer Platzhalter-Logik

Mit einer neuen, zusätzlichen Platzhalter-Logik in *Document* soll es
ermöglicht werden, Kurz-Formen von Platzhaltern zu definieren, die
intern auf die bisherige Notation übersetzt werden (quasi ein
preparse-Schritt im Ablauf-Diagramm des Page-Controllers).

#### Beispiel: Bisher

``` html4strict
<section class="<template:placeholder name="css-class"/>">
    <h3><template:placeholder name="headline"/></h3>
    <a href="<template:placeholder name="more-link"/>">
        <template:placeholder name="more-label"/>
    </a>
    <p>
        <template:placeholder name="intro-text" />
    </p>
    <div>
        <template:placeholder name="detail-text" />
    </div>
</section>
```

#### Beispiel: Zukünftig

``` html4strict
<!--
Place holder variants:
- ${{css-class}}
- {{css-class}}
- ${css-class}  // preferred due to only one bracket at start/end (also because similar to PlaceHolderTag implementation
- {%css-class%}
-->

<section class="${css-class}">
    <h3>${headline}</h3>
    <a href="${more-link}">
        ${more-label}
    </a>
    <p>
        ${intro-text}
    </p>
    <div>
        ${detail-text}
    </div>
</section>
```

#### Implementierungsidee

Wie auch bei den "normalen" Platzhaltern sollte die kurze Schreibweise
in "echte" Tags übersetzt werden. Beispiel:

``` html4strict
${intro-text} --> <*:placeholder name="intro-text" />
```

### Einführung einer einfachen DSL für den Zugriff auf Objekte

#### Beschreibung

**Template:**

``` html4strict
<!--
Object access:
- $(foo)          // display variable (non array/object or object implementing the __toString() method)
- $(foo[4])       // display 5th element of an numeric array
- $(foo['bar'])   // display the "bar" key value of an array, similar to PHP $foo['bar']
- $(foo->bar)     // display the public object property "bar"
- $(foo->bar())   // display the return value of method "bar"
-->
<section class="$(element->getCssClass())">
   <h3>$(element->getHeadline())</h3>
   <a href="$(element->getMoreLink()->getUrl())">
      $(element->getMoreLink()->getLabel())
   </a>
   <p>
      $(element->getIntroText())
      $(element->introText) // will be tried to resolve to a get method if field is not publically accessable
   </p>
   <div>
      $(element->getDetailText())
   </div>
</section>
```

**Controller:**

``` php
class FooController extends BaseDocumentController {

   public function transformContent() {
      $this->setTemplateProperty(element, $this->getElement());
   }

   ...

}
```

#### Implementierungsidee

Wie auch bei den Platzhaltern sollte die kurze Schreibweise in "echte"
Tags übersetzt werden. Beispiel:

``` html4strict
$(element->getIntroText()) --> <*:dynamic-evaluation-tag expression="element->getIntroText()" />
```

``` php
namespace APF\core\expression;

use APF\core\pagecontroller\BaseDocumentController;
use APF\core\pagecontroller\Document;

class DynamicLanguageExpressionExecutor {

   private $document;
   public function __construct($expression) {
   }

   public function setDocument(Document $document) {
      $this->document = $document;
   }

   public function getResult() {

      $property = substr(...);
      $this->document->getProperty($property);

      return ...;
   }

}


class DynamicEvaluationTag extends Document {

   public function transform() {
      $exec = new DynamicLanguageExpressionExecutor($this->getAttribute('expression'));
      $exec->setDocument($this->getParentObject());
      return $exec->getResult();
   }

}

class FooController extends BaseDocumentController {

   public function transformContent() {
      $this->setTemplateProperty('foo', $this->getElement());
   }

   private function getElement() {
      return ...;
   }

   private function setTemplateProperty($name, $object){
      $this->getDocument()->setProperty($name, $object);
   }

}
```

## Referenzen

-   <http://fabien.potencier.org/article/34/templating-engines-in-php>

## Implementierungsstatus

Die erste Implementierung des Features ist ab Revision \#2698 im SVN und
ab dem 01.02.2014 im aktuellen [2.1.
Snapshot](http://files.adventure-php-framework.org/snapshot/apf-2.1-snapshot-php5.tar.gz)
verfügbar. <languages />