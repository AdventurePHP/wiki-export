## Beschreibung

Sinn und Zweck der Taglib *html:fallbackimport* aus dem namespace
*APF\\tools\\html\\taglib* ist das Einbinden von Kind-Templates mit der
Möglichkeit, bei einem Fehler auf ein anderes Template auszuweichen. Die
Taglib erfüllt also eine ähnliche Aufgabe wie *core:importdesign*,
benutzt auch deren Funktionalität und weist die selben Attribute auf.
Für die Funktionalität des Fallbacks, die durch Abfangen einer
*IncludeException* aus *ImportTemplateTag::onParseTime()* realisiert
wird, wurden darüber hinaus weitere Attribute ergänzt:

## Attribute

Hier werden nur die Attribute beschrieben, die nicht in der Taglib
*core:importdesign*
([1](http://adventure-php-framework.org/Seite/046-Standard-TagLibs#Chapter-1-2-Importdesign))
enthalten sind!

fallback:
Gibt den Namen eines Templates im selben Namespace an, das als Fallback
eingesetzt wird.

<!-- -->

fallbackTemplate & fallbackNamespace:
Kann anstelle des Attributs *fallback* verwendet werden, wenn das
Template in einem anderen Namespace liegt.

<!-- -->

send404Header:
Legt fest, ob im Falle eines Fallbacks ein HTTP-Status-Header 404
gesendet werden soll. Vorgesehen zur Realisierung von 404-Fehlerseiten
in Verbindung mit dem *core:importdesign*-Attribut *incparam*. Der
Header wird gesendet, wenn das Attribut gesetzt ist und einen anderen
Wert als *"false"* hat.

<!-- -->

fallbackFailureIgnore & errormsg:
Hat dieses Attribut den Wert *"true"* oder *"yes"* wird bei einem nicht
gefundenen Fallbacktemplate keine *IncludeException* geworfen, sondern
der Wert des Attributs *errormsg* angezeigt.

## Verwendungs-Beispiel

``` xml
<core:addtaglib
   name="fallbackimport"
   prefix="html"
   class="APF\tools\html\taglib\FallbackImportTemplateTag"
/>
...
<div id="content">
   <html:fallbackimport
      namespace="APF\sites\demo\pres\templates"
      incparam="page"
      template="[ page=index ]"
      fallback="notFound"
      send404Header="true"
   />
</div>
...
```

Dies ist ein klassisches Verwendungsbeispiel. Das Fallbacktemplate
*notFound* wird eingebunden, wenn das mit dem GET-Parameter *page*
angegebene Template nicht existiert. Ausserdem wird ein 404 HTTP-Header
gesendet.

``` xml
<html:fallbackimport
   namespace="APF\sites\demo\pres\templates"
   template="leftbox"
   fallbackNamespace="APF\sites\demo\pres\templates\fallbacks"
   fallbackTemplate="leftbox"
/>
```

Hier liegt das Fallback-Template in einem anderen Verzeichnis
(Namespace). Obwohl es den selben (Datei-)namen hat, muss der Name des
Templates erneut angegeben werden!

``` xml
<html:fallbackimport
   namespace="APF\sites\demo\pres\templates"
   template="comment"
   fallbackIgnoreFailure="yes"
   errormsg="Kein Kommentar verfügbar"
/>
```

Da hier kein Fallback-Template angegeben ist, wird direkt zum Anzeigen
des Fehlers übergegangen.

## Abhängigkeiten

-   APF-Version &gt;= 1.12
-   APF\\tools\\http\\HeaderManager