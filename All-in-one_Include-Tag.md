<div style="border: 2px solid #fb3; background: #ff8; color: #333; margin: 1em 0; min-height: 3em; padding: 10px 10px 10px 10px;">

Bitte beachten Sie, dass diese Dokumentation für die Versionen 1.X und
2.X **jedoch nicht für 3.X** gilt. Ab Version 3.0 werden Tags generell
global registriert und ein *All-in-one Include-Tag* kann durch eine
registrierung der Tags in der Bootstrap-Datei ersetzt werden.

</div>

Es kommt immer wieder die Frage auf, ob es möglich ist, eine Taglib zur
Einbindung eines Templates zu haben, in dem alle Taglibs des APF bereits
bekannt sind und kein

``` php
<core:addtaglib .../>
```

mehr notwendig ist. Durch die generische Implementierung des
[Page-Controller](http://adventure-php-framework.org/Seite/098-Pagecontroller)
ist es zu jeder Zeit möglich, das Verhalten des Aufbaus des
APF-DOM-Baumes und die DOM-Elemente selbst zu beeinflussen.

Für diese Aufgabe nutzen wir den bereits vorhandenen Tag
[<core:importdesign />](http://adventure-php-framework.org/Seite/046-Standard-TagLibs#Chapter-1-2-Importdesign)
als Grundlage und erweitern diesen so, dass er die von uns gewünschten
Taglibs zusätzlich zu den bisherigen kennt.

Das funktioniert wir folgt:

``` php
class AllImportTemplateTag extends ImportTemplateTag {
   public function __construct(){
      $this->tagLibs[] = new TagLib('APF\tools\form\taglib\HtmlFormTag','html','form');
      $this->tagLibs[] = new TagLib('APF\tools\media\taglib\MediaInclusionTag','html','mediastream');
      ...
      $this->tagLibs[] = new TagLib('MY\namespace\AllTemplateTag','all','importdesign');
   }
}
```

Dem Konstruktor der Klasse kann man entnehmen, dass die Taglib selbst
ebenfalls in der Reihe der bekannten Taglibs erscheint. Dies ist
insofern notwendig, als dass der Tag <all:importdesign /> auch in durch
den Tag eingebunden Templates bekannt sein soll.

Um den Mechanismus zu nutzen, muss der Tag einmalig im Basis-Template
bekannt gemacht und zur Einbindung eines Templates genutzt werden.
Anschließend ist der Tag im kompletten Baum verfügbar und die
hinzugefügten Tags sind ohne zusätzliche Einbindung bekannt. Dies kann
per

``` html4strict
<core:addtaglib class="MY\namespace\AllTemplateTag" prefix="all" name="importdesign" />
<all:importdesign namespace="..." template="..." />
```

erledigt werden.

### Taglibs gemeinsam hinzufügen

Die gleiche Technik lässt sich auch anwenden, wenn man kein Template
importieren möchte, jedoch mehrere Taglibs auf einmal bekannt machen
möchte. Dazu erstellen wir uns ein neues Tag, welches zur Parse-Time die
gewünschten Taglibs dem Vaterobjekt (-&gt; dies ist das Template, in dem
die Taglib aufgerufen wird) hinzufügt. Ein Beispiel dafür ist das Tag
*APF\\extensions\\apfelsms\\pres\\taglibs\\SMSAddAllTag*:

``` php
class SMSAddAllTag extends Document {


   public function onParseTime() {

      // Vaterobjekt beziehen:
      $doc = $this->getParentObject();

      $namespace = 'APF\extensions\apfelsms\pres\taglibs\\';
      $prefix = 'sms';

      //Taglibs erzeugen:
      $importDesignTaglib = new TagLib($namespace . 'SMSImportDesignTag', $prefix, 'importdesign');
      $navTaglib = new TagLib($namespace . 'SMSNavTag', $prefix, 'nav');
      // weitere...

      // Taglibs dem Vater hinzufügen:
      $doc->addTagLib($importDesignTaglib);
      $doc->addTagLib($navTaglib);
      // weitere ...
   }


   public function transform() {


      return ''; // we are just dummy ;)
   }

}
```