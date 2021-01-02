<div style="border: 2px solid orange; padding: .5em; background: #efefef;">

Die Implementierung des Proposals wurde mit Issue
<http://tracker.adventure-php-framework.org/view.php?id=283> vorgenommen
und wird in Release 3.2 veröffentlicht.

</div>

Dieses Proposal beschäftigt sich mit der Idee, gewisse Entscheidungen
zur Darstellung von Inhalten im Template deklarieren zu können. Dies
soll Controller schlanker gestalten und dadurch die Wartbarkeit erhöhen.

### Template

Das folgende Template zeigt folgende Konstrukte:

-   die Ausgabe eines Inhalts eines View-Models (*<dyn:expr />* oder
    *${..}*-Syntax)
-   ein Platzhalter, der nur unter der Bedingungen ausgegeben wird
    (*<cond:placeholder />*)
-   ein Template, das nur ausgegeben wird, wenn der im Attribut
    *expression* evaluierte Wert der Bedingung des *condition*-Attributs
    entspricht (*<cond:template />*):

``` html4strict
<core:addtaglib
   class="APF\core\expression\taglib\ExpressionEvaluationTag"
   prefix="dyn"
   name="expr"
/>
<core:addtaglib
   prefix="cond"
   name="placeholder"
   class="SB\pres\taglib\ConditionalPlaceHolderTag"
/>
<core:addtaglib
   prefix="cond"
   name="template"
   class="SB\pres\taglib\ConditionalTemplateTag"
/>

<dyn:expr
   expression="model[0]->getFoo()"
/>

<cond:placeholder name="text-with-markup">
   <p style="border: 1px solid red;">
      ${content}
   </p>
</cond:placeholder>

<cond:template name="expr-template" expression="content->getFoo()" condition="notEmpty()">
   <p style="border: 1px solid red;">
      content->displayIt() == true
      <br />
      content->getFoo(): ${this->getParentObject()->getData('content')->getFoo()}
      <br />
      Dynamic place holder: ${dyn-place-holder}
   </p>
</cond:template>
```

### Controller

Im Controller werden lediglich die Inhalte als View-Model ins Dokument
eingesetzt bzw. zu Demonstrations-Zwecken ein weiterer Platzhalter im
Template befüllt:

``` php
class IfController extends BaseDocumentController {

   public function transformContent() {

      $this->getDocument()->setData('model', [new TestModel(), new TestModel()]);

      $this->setPlaceHolder('text-with-markup', 'test');

      $this->getDocument()->setData('content', new TestModel());

      $template = $this->getTemplate('expr-template');
      $template->setPlaceHolder('dyn-place-holder', 'dynamic text from controller "' . get_class($this) . '"');
      $template->transformOnPlace();

   }

}
```

### Tags

Die Implementierung der beiden Tags gestaltet sich wie folgt:

``` php
class ConditionalPlaceHolderTag extends Document implements PlaceHolder {

   protected $placeHolderContent;

   protected $parsingDone = false;

   public function onParseTime() {
      parent::onParseTime();
      $this->parsingDone = true;
   }

   public function transform() {

      if (empty($this->placeHolderContent)) {
         return '';
      }

      $contentName = $this->getAttribute('content-name', 'content');

      // inject place holder content into data attribute to make it accessible within the tag for displaying
      if (is_string($this->placeHolderContent)) {
         // automatically replace simple place holders
         $this->setPlaceHolder($contentName, $this->placeHolderContent);
      } elseif (is_array($this->placeHolderContent) || is_object($this->placeHolderContent)) {
         // inject content into a data attribute for further access
         $this->setData($contentName, $this->placeHolderContent);
      }

      return $this->transformChildrenAndPreserveContent();

   }

   public function setContent($content) {

      if ($this->parsingDone) {
         // re-direct place holder value separate field to allow injection into data attribute
         $this->placeHolderContent = $content;
      } else {
         // set "normal" content to real DOM node content to allow markup generation
         parent::setContent($content);
      }

      return $this;
   }

}
```

``` php
class ConditionalTemplateTag extends TemplateTag {

   public function transform() {

      $result = ExpressionEvaluator::evaluate(
            $this->getParentObject(),
            $this->getRequiredAttribute('expression')
      );

      if ($this->evaluateResult($result, $this->getAttribute('condition', 'true()'))) {
         return parent::transform();
      }

      return '';

   }

   protected function evaluateResult($result, $condition) {
      switch ($condition) {
         case strpos($condition, 'true') !== false:
            return $result === true;
            break;
         case strpos($condition, 'false') !== false:
            return $result === false;
            break;
         case strpos($condition, 'empty') !== false:
            return empty($result);
            break;
         case strpos($condition, 'notEmpty') !== false:
            return !empty($result);
            break;
         case strpos($condition, 'matches') !== false:
            return $result == $this->getArgument($condition);
            break;
         case strpos($condition, 'longerThan') !== false:
            return strlen($result) >= $this->getArgument($condition);
            break;
         case strpos($condition, 'shorterThan') !== false:
            return strlen($result) < $this->getArgument($condition);
            break;
         case strpos($condition, 'length') !== false:
            return strlen($result) == $this->getArgument($condition);
            break;
         default:
            return false;
            break;
      }
   }

   protected function getArgument($condition) {
      $open = strpos($condition, '(');
      $close = strrpos($condition, ')', $open);

      return trim(substr($condition, $open + 1, $close - $open - 1));
   }

}
```