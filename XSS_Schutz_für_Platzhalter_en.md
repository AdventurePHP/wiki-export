## Introduction

With the **XssProtectionInputFilter**, the APF offers a very effective
protection. Details can be taken from [XSS Schutz via
InputFilter](/XSS_Schutz_via_InputFilter "wikilink").

In order to set up an effective protection also with including external
data sources (i.e. feeds) the [Extended templating
syntax](http://adventure-php-framework.org/Page/161-Extended-template-functionality#Chapter-3-Extending-the-syntax)
can be used to extend the place holder tag containes in the APF with XSS
filtering.

## Solution

First, the place holder implementation contained in the APF will be
enhanced with an XSS protection filtering. This can simply be done by
extending the *PlaceHolderTag* class:

``` php
class XssProtectionPlaceHolderTag extends PlaceHolderTag {

   public function transform() {
      return strip_tags(
            html_entity_decode(
                  parent::transform(), ENT_QUOTES, Registry::retrieve('APF\core', 'Charset')
            )
      );
   }

}
```

The [APF
parser](http://adventure-php-framework.org/Page/162-Parser-secrets)
provides an API to register custom *TemplateExpression* implementations.
To register a template expression for the previously implemented place
holder tag, a template expression implementation is required:

Please create the following class:

``` php
class XssProtectionPlaceHolderTemplateExpression extends PlaceHolderTemplateExpression {

   public static function getDocument($token) {
      $placeHolder = new XssProtectionPlaceHolderTag();
      $placeHolder->setAttribute('name', $token);

      return $placeHolder;
   }

}
```

The new template expression can now be registered to replace the
(standard) place holder expression:

``` php
Document::clearTemplateExpressions();
Document::addTemplateExpression(XssProtectionPlaceHolderTemplateExpression::class);
Document::addTemplateExpression(DynamicTemplateExpression::class); // Standard expression from bootstrap.php
```

Now, all place holder values within the application will be filtered
through *strip_tags()* and *html_entity_decode()* providing an
additional level of security.

## Links

The functionality described within this article has been requested
through tracker entry
[ID\#147](http://tracker.adventure-php-framework.org/view.php?id=147).
<languages />