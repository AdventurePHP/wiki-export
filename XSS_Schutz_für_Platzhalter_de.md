## Einführung

Zum Schutz vor XSS-Attacken bietet das APF mit dem
**XssProtectionInputFilter** einen wirksamen Schutz. Details hierzu
lassen sich unter [XSS Schutz via
InputFilter](/XSS_Schutz_via_InputFilter "wikilink") nachlesen.

Um auch bei Einbindung von externen Datenquellen (z.B. Feeds) einen
wirksamen Schutz zu gewährleisten, lässen sich die [Erweiterte
Template-Syntax](http://adventure-php-framework.org/Seite/161-Erweiterte-Template-Funktionen#Chapter-3-Erweiterung-der-Syntax)
dazu nutzen, den im APF enthaltenen Platzhalter-Tag um potentielle
XSS-Fragmente bei der Generierung der Ausgabe zu filtern.

## Lösung

Zunächst wird die im APF enthaltene Platzhalter-Implementierung um den
XSS-Schutz erweitert. Dies kann mit Hilfe einer neuen und von
*PlaceHolderTag* abgeleiteten Klasse erledigt werden:

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

Der
[APF-Parser](http://adventure-php-framework.org/Seite/162-Parser-Secrets#Chapter-4-Erweiterte-Templating-Syntax)
stellt zur Registrierung von eigenen
*TemplateExpression*-Implementierung eine geeignete Schnittstelle zur
Verfügung. Hierzu ist eine vom gleichnamigen Interface abgeleitete
Implementierung für den zuvor implementierten Platzhalter-Tag notwendig.

Hierzu erstellen Sie bitte folgende Klasse:

``` php
class XssProtectionPlaceHolderTemplateExpression extends PlaceHolderTemplateExpression {

   public static function getDocument($token) {
      $placeHolder = new XssProtectionPlaceHolderTag();
      $placeHolder->setAttribute('name', $token);

      return $placeHolder;
   }

}
```

Die neue Template-Expression lässt sich nun wie folgt registrieren bzw.
den bisherigen (Standard-)Platzhalter ersetzen:

``` php
Document::clearTemplateExpressions();
Document::addTemplateExpression(XssProtectionPlaceHolderTemplateExpression::class);
Document::addTemplateExpression(DynamicTemplateExpression::class); // Standard Expression aus bootstrap.php
```

Anschließend werden alle Platzhalter-Werte in der Applikation durch die
PHP-Funktionen *strip_tags()* und *html_entity_decode()* gefiltert
und die Applikation dadurch noch zusätzlich geschützt.

## Verweise

Die in diesem Artikel beschriebene Funktionalität wurde im Rahmen des
Tracker-Eintrags
[ID\#147](http://tracker.adventure-php-framework.org/view.php?id=147)
angefordert. <languages />