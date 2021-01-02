This page describes a feature request for enhanced templating. The
improvement is targeted to the 2.X code tree.

## Current situation

1.  Place holders can only be defined via *&lt;\*:placeholder
    name=""/&gt;*.
    1.  More typing than Smarty syntax
    2.  Syntax highlighting of IDE sometimes broken
2.  Setting place holders within controllers takes a significent part of
    the code (especially with complex objects or object structures). A
    simple templating language to access objects and their properties
    would ease coding.

## Paradigm

-   Separation of logic and view should be enforces also in the future
    meaning no control structures in templates.

## Suggestion

### Introduction of new place holder logik

Using the new, additional place holder logic in class *Document*
defining short forms of place holders should be possible. During
analysis phase they are translated to known structures (e.g. within a
pre-parse steb of the flow diagram of the page controller).

#### Example: as is

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

#### Example: Future

``` html4strict
<!--
Place holder variants:
- ${{css-class}}
- {{css-class}}
- ${css-class}  // preferred due to only one bracket at start/end (also because similar to PlaceHolderTag implementation)
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

#### Implementation idea

As with "normal" place holders the short notation should be transpated
to real tags. Example:

``` html4strict
${intro-text} --> <*:placeholder name="intro-text" />
```

### Introduction of a simple DSL object access

#### Description

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

#### Implemention idea

As with place holders the short notation should be translated to "real"
tags. Example:

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

## References

-   <http://fabien.potencier.org/article/34/templating-engines-in-php>

## Implementation status

The first implementation of the feature is available in SVN as if
revision \#2698 and within the current [2.1.
Snapshot](http://files.adventure-php-framework.org/snapshot/apf-2.1-snapshot-php5.tar.gz).
<languages />