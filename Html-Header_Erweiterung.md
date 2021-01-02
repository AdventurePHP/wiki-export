# HtmlHeader (ab 1.15)

Mit Version 1.15 des APF wurden die Erweiterungen HtmlHeader,
JsCssPackager und JsCssInclusion von [Ralf
Schubert](/Benutzer:Screeze "wikilink") zusammengeführt, da diese
bereits vorher eng verflochten waren. Diese Erweiterung ist daher ab
sofort nicht nur in der Lage jedes beliebige HTML-Element nachträglich
in den

<head>

-Bereich einer Seite zu kopieren, sondern auch Javascript- und
CSS-Dateien von beliebigen Plätzen innerhalb des APF-Ordners
auszuliefern, sowie mehrere JS oder CSS Dateien zu einer einzigen Datei
zusammenzufassen, zu minimieren, zu komprimieren und zu cachen. Dieses
Vorgehen spart eine Menge Performance und Ladezeit. Ebenfalls ist es nun
möglich sämtliche Javascript-Knoten an das Ende des Html-Body zu
injizieren, anstatt in den Head-Bereich, was sich oftmals positiv auf
die gefühlte Performance auswirken kann.

Sofern sie noch auf einer vorherigen Version arbeiten, gehen sie bitte
zu [htmlheader (vor
1.15)](/Html-Header_Erweiterung_(vor_1.15) "wikilink"), [jscssinclusion
(vor 1.15)](/Javscript-_und_CSS-Einbindung "wikilink") bzw.
[JsCssPackager (vor
1.15)](/Javascript-_und_Css-Paketmanager "wikilink").

# Head-Knoten nachträglich hinzufügen

In dynamischen Anwendungen ist es oft notwendig von einem beliebigen
Punkt aus nachträglich Daten im Html-Head der Seite anzufügen, obwohl
dieser eigentlich nicht im aktuellen Einflussbereich liegt. Die
HtmlHeader Erweiterung wurde zu genau diesem Zweck geschaffen. Mit ihr
kann an jeder beliebigen Stelle der

<head>

-Bereich ergänzt werden, egal ob aus einem Template heraus, von einem
Manager oder durch einen Documentcontroller. Es können so ziemlich alle
möglichen HTML-Head-Elemente hinzugefügt werden, angefangen vom

<title>

-Tag bis hin zum Einbinden von Javascript- und CSS-Dateien. Sollte ein
benötigter Tag fehlen ist es Problemlos möglich diesen Nachträglich
hinzuzufügen.

Die JS- und CSS-Dateien können dabei statisch definiert werden, oder
mithilfe einer Action oder als Paket ausgeliefert werden, deren URLs
automatisch generiert werden.

Im

<head>

-Bereich der Webseite muss der <htmlheader:gethead /> Tag platziert
werden, welcher sich mithilfe eines Output-Filters um die Ausgabe der
nachträglich hinzugefügten Informationen kümmert:

``` html4strict
<head>
   <core:addtaglib
      class="APF\extensions\htmlheader\pres\taglib\HtmlHeaderGetHeadTag"
      prefix="htmlheader"
      name="gethead"
   />

  <htmlheader:gethead />
</head>
```

Sofern Javascript Dateien an das **Ende des Html-Body** angefügt werden
sollen später, so muss vor dem schließenden

</body>

-Tag die getbodyjs-Taglib eingefügt werden:

``` html4strict
<body>
   ...
   <core:addtaglib
      class="APF\extensions\htmlheader\pres\taglib\HtmlHeaderGetBodyJsTag"
      prefix="htmlheader"
      name="getbodyjs"
    />

   <htmlheader:getbodyjs />
</body>
```

## In Templates

Es stehen einige Taglibs zur Verfügung, mit welchen Informationen in
Templates hinzugefügt werden können. Sollte ein Anwendungsfall nicht
abgedeckt sein, können Problemlos eigene Taglibs geschrieben werden,
welche intern die API der Erweiterung ansprechen. Die Taglibs befinden
sich alle unter extensions::htmlheader::<pres::taglib>, besitzen das
prefix "htmlheader" und müssen vor der Verwendung mittels
<core:addtaglib /> hinzugefügt werden. In einem Core-Template würde das
bekannt machen für htmlheader:addcss so aussehen: **Examples:**

``` html4strict
<core:addtaglib
   class="APF\extensions\htmlheader\pres\taglib\HtmlHeaderAddCssTag"
   prefix="htmlheader"
   name="addcss"
/>
[...]
```

**Es stehen folgende Taglibs zur Verfügung:**

<div style="border: 1px solid gray; background: #F9F9F9; padding: 5px; font-weight: bold;">

Die Reihenfolge der Js- und Css-Dateien und Pakete kann seit Version
1.14 mit dem optionalen Attribut <em>priority</em> beeinflusst werden.
Je größer die angegebene Zahl (Integer) desto weiter oben wird die Datei
eingefügt.
Beispiel: Element 1 (Prio 0), Element 2 (Prio 10), Element 3 (Prio 5)
-&gt; wird zu -&gt; Element 2, Element 3, Element 1.
Wird das Attribut nicht angegeben, wird 0 als Standardwert gesetzt.

</div>



<div style="border: 1px solid blue; background: lightBlue; padding: 5px; font-weight: bold;">

Seit Version 1.15 kann für sämtliche Javascript-Knoten (auch
JsCssPackager-Pakete vom Typ <em>js</em>) das optionale Attribut
<em>appendtobody</em> auf <em>true</em> gesetzt werden, um Javascript
Dateien oder Codes an das Ende des Body zu setzen, anstatt in den
Head-Bereich. (siehe getbodyjs-Taglib oben!) Das wird meist gemacht um
dem User die Seite bereits anzeigen zu können, wenn noch nicht der
komplette Javascript-Code abgerufen wurde.

</div>

### Title-Tag

Der Titel der Webseite (

<title>

) kann mithilfe der addtitle-Taglib definiert werden. Wenn das optionale
Attribut *"append"* auf **"true"** gesetzt wird, wird der Inhalt des
Tags an einen eventuell bereits existierenden Titel angehängt,
andernfalls wird der vorhandene Titel ersetzt.

``` html4strict
<htmlheader:addtitle append="true">
Titel der Webseite
</htmlheader:addtitle>
```

### Statischer Js- & Css-Code

Statischer Js und Css Code (inline, nicht in externer Datei ausgelagert)
kann wie folgt hinzugefügt werden:

``` html4strict
<htmlheader:addcsscontent>
.exampleClass {
    border: 1px solid red;
}
</htmlheader:addcsscontent>
<htmlheader:addjscontent>
$('#foo').appendClass('bar');
</htmlheader:addjscontent>
```

### Statische Js- & Css-Dateien

Statische Js- und Css-Dateien können wie folgt hinzugefügt werden:

``` html4strict
<htmlheader:addstaticcss file="http://media.adventure-php-framework.org/css/apf.css" />
<htmlheader:addstaticjs file="http://maps.google.com/maps/api/js?sensor=false" />
```

### Dynamische Js- & Css-Dateien // Einbindung per FrontController-Action

Es besteht die Möglichkeit die URL der Js und Css Dateien automatisch
generieren zu lassen, und die Dateien über eine FC-Action einbinden zu
lassen, wenn diese sich z.B. außerhalb des von außen zugänglichen
Bereichs befinden.

Hierfür stehen die beiden Taglibs <em>htmlheader:addcss</em> und
<em>htmlheader:addjs</em> zur Verfügung. Die Funktionsweise wird nur
anhand der addjs-Taglib gezeigt, da addcss nach dem gleichen Schema
funktioniert.

Zur Verfügung stehen die Attribute:

-   url: Kann benutzt werden um Dateien von einem fremden Server
    einzubinden.
-   folder: Wenn eine Datei von einem fremden Server eingebunden wird,
    wird hiermit namespace zur Datei auf dem Server definiert, mit ::
    als / Trennung.
-   namespace: Der Namespace der Datei, wenn diese auf dem selben Server
    liegt.
-   filename: Der Dateiname ohne Dateiendung.
-   rewriting: Optional. Angeben, wenn explizit eine URL generiert
    werden soll, die rewriting an-/abgeschalten hat. Standardmäßig wird
    die aktuelle Einstellung der Webseite verwendet.
-   fcaction: Optional. Wenn keine FC-Action zum Ausliefern verwendet
    werden soll, diesen wert auf false setzen. (Standard: true)

``` html4strict
// Einbinden einer JS-Datei des aktuellen Servers mithilfe einer FC-Action.
<htmlheader:addjs namespace="APF\sites\example\pres\frontend\static\js" filename="jquery.min" />

// Einbinden einer externen Datei mit deaktiviertem Url Rewriting und ohne FC-Action an das ENDE des <body>-Bereichs (siehe Infobox oben)
<htmlheader:addjs
    url="http://static/"
    folder="js\anything"
    filename="jquery.min"
    rewriting="false"
    fcaction="false"
    appendtobody="true"
/>
```

### Js- und CSS-Pakete

Mithilfe des in diese Erweiterung integrierten JsCssPackagers können
viele Js- oder CSS-Dateien zu je 1 Datei zusammengefasst werden. Für die
Konfiguration dieses Verhaltens scrollen sie bitte zu [Js- & Css-Pakete
definieren](/#Js-_&_Css-Pakete_definieren "wikilink"). Hier wird die
Einbindung eines Pakets innerhalb eines Templates beschrieben.

Das <em>name</em>-Attribut entspricht dem Paketname, welcher in der
Konfiguration als Sektionsname verwendet wird. Mögliche <em>type</em>
Angaben sind "js" und "css".

``` html4strict
<htmlheader:addpackage
    name="form_clientvalidators_all"
    type="js"
/>
<htmlheader:addpackage
    name="mystylesheetpackage"
    type="css"
/>
```

### CssImages (Favicons etc.)

Um ein Favicon hinzuzufügen gibt es die Taglib htmlheader:addcssimage

``` html4strict
<htmlheader:addcssimage rel="icon" href="favicon.png" type="image/png" />
```

## In Controllern und anderen PHP-Dateien

Die Erweiterung besteht aus einer Vielzahl von Html-Knoten ("Nodes") im
Namespace *APF\\extensions\\htmlheader\\biz*. Die jeweils gewünschten
Knoten müssen zuerst eingebunden werden, z.B. über:

``` php
use APF\extensions\htmlheader\biz\DynamicJsNode;
```

**Es stehen folgende Nodes zur Verfügung:**

-   BaseUrlNode: Um <base href="" /> zu definieren
-   CanonicalNode: Um <link rel="canonical" href="" /> zu definieren
-   ConditionalDynamicCssNode
-   ConditionalStaticCssNode
-   CssContentNode
-   CssImageNode
-   CssPackageNode
-   DynamicCssNode
-   DynamicJsNode
-   HttpMetaNode
-   JsContentNode
-   JsPackageNode
-   LanguageMetaNode
-   RefreshNode
-   SimpleMetaNode
-   SimpleTitleNode
-   StaticCssNode
-   StaticJsNode

Anschließend wird eine Instanz des HtmlHeaderManager bezogen, welcher
für die Speicherung der Knoten zuständig ist:

``` php
// Get an instance of HtmlHeaderManager:
$HHM = $this->getServiceObject('APF\extensions\htmlheader\biz\HtmlHeaderManager');
```

Nun kann eine Instanz jedes gewünschten Knoten erzeugt werden, und dem
HtmlHeaderManager übergeben werden (import der Nodes aus
Übersichtsgründen weggelassen):

``` php
// Add a refresh on index.php?test=abc, with a delay of 5 seconds:
$HHM->addNode(new RefreshNode('index.php', 5, array("test" => "abc")));

// Add a title
$HHM->addNode(new SimpleTitleNode("Example title"));

//Get instance, configure in constructor and add to HtmlHeaderManager:
$CssNode = new DynamicCssNode($url, $namespace, $filename, $rewriting, $fcaction);
$HHM->addNode($CssNode);

// Add a simple description meta tag
$HHM->addNode(new SimpleMetaNode('description',$description));

// Add a http meta tag definition
$HHM->addNode(new HttpMetaNode('content-type','text/html; charset=utf-8'));

// Define and add a JsCSSPackager-Package (Javascript) to HHM
$PackageNode = new JsPackageNode($url, $name, $type, $rewriting);
$HHM->addPackage($PackageNode);
```

<div style="border: 1px solid gray; background: #F9F9F9; padding: 5px; font-weight: bold;">

Wenn die PHP-API verwendet wird, kann die Reihenfolge der Nodes
ebenfalls beeinflusst werden. Hierzu kann die
<em>setPriority()</em>-Methode verwendet werden. Beispiel:

``` php
$node = new SimpleMetaNode('description',$description);
$node->setPriority(20);
$HHM->addNode($node);
```

</div>



<div style="border: 1px solid blue; background: lightBlue; padding: 5px; font-weight: bold;">

Wenn die PHP-API verwendet wird, können ebenfalls Javascript-Nodes oder
Pakete anstatt zum

<head>

-Bereich zum Ende des

<body>

hinzugefügt werden. Hierzu kann die <em>setAppendToBody()</em>-Methode
verwendet werden. Beispiel:

``` php
$PackageNode = new JsPackageNode($url, $name, $type, $rewriting);
$PackageNode->setAppendToBody(true);
$HHM->addPackage($PackageNode);
```

</div>

# Js- & Css-Dateien/Pakete ausliefern

Um Js- & Css-Dateien per FC-Action ausliefern zu können, beziehungsweise
um entsprechende Pakete ausliefern zu können, muss eine
FrontController-Action konfiguriert werden. Unter
<em>config/extensions/htmlheader/{CONTEXT}/{ENVIRONMENT}_actionconfig.ini</em>
muss daher folgende Konfiguration abgelegt werden:

``` ini
[JsCss]
ActionClass = "APF\extensions\htmlheader\biz\actions\JsCssInclusionAction"
```

Desweiteren können einfache Js- & Css-Dateien ebenfalls geshrinkt
(verkleinert) werden vor dem ausliefern, um Bandbreite zu sparen. Dies
kann in der Datei
<em>config/extensions/htmlheader/biz/{CONTEXT}/{ENVIRONMENT}_JsCssInclusion.ini</em>
festgelegt werden:

``` ini
[General]
EnableShrinking = "true"
```

**Diese Einstellung gilt nicht für Pakete, Pakete können einzeln
konfiguriert werden, siehe dazu die Paketkonfiguration im nächsten
Abschnitt.**

# Js- & Css-Pakete definieren

Sollen Pakete ausgeliefert werden, müssen diese vorher konfiguriert
werden. Hierzu muss die Datei
<em>config/extensions/htmlheader/biz/{CONTEXT}/{ENVIRONMENT}_JsCssPackager.ini</em>
angelegt werden.
Für jedes Paket muss dort eine Sektion angelegt werden, mit einem frei
wählbaren Paketname als Sektionsname.

Beispiel:

``` ini
[DefaultTheme]
ClientCacheDays = "7"           ; How long should it be cached on client?
ServerCacheMinutes = "10080"    ; How long should it be cached on server?
EnableShrinking = "true"        ; Shrink the code in the package?
PackageType = "css"              ; Possible types: 'js' and 'css'
; The files the package contains. Filename without extension!
Files.1.Namespace = "develovisioncms::themes::default::css"
Files.1.Filename = "menu"
Files.2.Namespace = "develovisioncms::themes::default::css"
Files.2.Filename = "style"
```

Jedes Paket kann individuell mit Cachingzeiten konfiguriert werden,
sowie Shrinking (de-)aktiviert werden. (Beim Shrinking werden unnötige
Zeichen aus den Dateien entfernt, daraus resultiert meistens ein
einzeiliger Code der für Menschen schwer lesbar ist, jedoch vom Browser
problemlos interpretiert werden kann)

In der Subsection <em>Files</em> wird für jede Datei eine Nummer
angegeben (Mehrfachdefinition einer Zahl ist nicht erlaubt und führt zu
unerwünschten Resultaten!), und jeweils Namespace und Dateiname ohne
Dateiendung angegeben.

Selbiges gilt für JS-Dateien, hier muss lediglich der
<em>PackageType</em> auf "js" geändert werden.

**Wichtig: Wenn Server Caching aktiviert wird, wird das fertige Paket
einmal komprimiert (gzip) und einmal unkomprimiert (für Clienten die
kein gzip beherrschen) zwischengespeichert, damit dies beim nächsten
Aufruf nicht erneut generiert werden muss und direkt ausgeliefert werden
kann. Hierfür wird der CacheManager des APF verwendet, dessen Doku
[HIER](http://adventure-php-framework.org/Seite/084-CacheManager)
erreichbar ist.
Für das Caching wird daher eine Konfiguration des CacheManagers
benötigt. Der Packager verwendet den Cache mit Name
"jscsspackager_cache", dieser muss in der CacheManager-Config definiert
werden. Es wird hierfür der TextCacheProvider empfohlen.(Siehe Doku des
CacheManagers)**

# Filtern von Js- & Css-Dateien/Paketen

Die ausgelieferten Js- & Css-Dateien können, ebenso wie die Pakete, vor
Auslieferung bzw. Server-Caching durch eigene Filter geschickt werden.
Filter werden jeweils global definiert, wobei JS- und CSS-Filter
getrennt definiert werden.

Es wird aktuell kein Filter mit dem APF geliefert, daher beschreibe ich
hier ein Beispiel aus einem meiner Projekte.

## Filter erstellen

Folgender Beispielfilter soll folgende Funktion erfüllen: Alle URLs in
CSS-Dateien die mit ./cms/ beginnen sollen umgeschrieben werden auf eine
mediaStream-Action, welche Inhalte aus einem vordefinierten Ordner
ausliefert. Dies wird im entsprechenden Projekt benutzt, um innerhalb
von Themes Bilder, welche sich im nicht-erreichbaren Bereich befinden
und die in Css-Dateien verwendet werden, ausliefern zu können.

Aus einer CSS-Definition wie folgender

``` css
.menu ul ul a.sub, .menu ul ul a.sub:visited {
    background: #cbcac7 url('./cms/themes/default/arrow_right_black.png') no-repeat;
}
```

soll daher folgendes werden:

``` css
.menu ul ul a.sub, .menu ul ul a.sub:visited {
    background: #cbcac7 url('http://localhost/APF/DevelovisionCMS/~/APF_tools_media-action/streamMedia/namespace/themes_default/filebody/arrow_right_black/extension/png') no-repeat;
}
```

Jeder Filter muss nur das ChainedContentFilter-Interface implementieren.
Ich nenne meinen Filter MediaStreamLinkFilter, und lege ihn unter dem
frei wählbaren namespace *develovisioncms\\cmscore\\filter* ab.

<em>develovisioncms/cmscore/filter/MediaStreamLinkFilter.php:</em>

``` php
<?php
use APF\tools\link\LinkGenerator;

/**
 * This filter is used when delivering css-files with JsCssInclusion.
 * Urls with the format ./cms/any/path/in/apps_path/folder/filename.extension
 * will be replaced with an absolute link to the mediastream-tag,
 * which delivers the file. Should be used for images which for example
 * could be used in background-definitions in the css files.
 *
 * @author Ralf Schubert <<a href="http://develovision.de">Develovision Webentwicklung</a>>
 */
class MediaStreamLinkFilter implements ChainedContentFilter {

    public function filter(FilterChain &$chain, $input = null) {

        $input = preg_replace_callback(
                '/url\([\'|"]\.\/cms\/(.*)\/(.*)\.(.*)[\'|"]\)/i', array('MediaStreamLinkFilter', 'replaceLink'), $input
        );

        // delegate filtering to the applied chain
        return $chain->filter($input);
    }

    public static function replaceLink($hits){
        return 'url(\'' . LinkGenerator::generateActionUrl(Url::fromString(CMS_BASEURL), 'tools_media', 'streamMedia', array(
            'namespace' => str_replace('/', '_', $hits[1]),
            'filebody' => $hits[2],
            'extension' => $hits[3]
        )) . '\')';
    }

}

?>
```

Der Filter muss die Methode <em>filter(FilterChain &$chain, $input =
null)</em> definieren, und diese muss mit <em>return
$chain-&gt;filter($input);</em> beendet werden, um andere Filter
weiterhin auszuführen! Als nächstes muss der Filter nurnoch für
CSS-Dateien registriert werden...

## Filter konfigurieren

Filter werden global in der Datei
<em>config/extensions/htmlheader/biz/{CONTEXT}/{ENVIRONMENT}_JsCssInclusion.ini</em>
definiert.

Javascript-Filter werden in der Sektion **JsFilter**, Css-Filter in der
Sektion *CssFilter* definiert. Ich habe keine Js-Filter, daher sieht
meine Config wie folgt aus (die General-Sektion, die bereits oben
erwähnt wurde habe ich hier weggelassen):

``` ini
[CssFilter]
MediaStreamLinkFilter.Namespace = "develovisioncms\cmscore\filter"
MediaStreamLinkFilter.Name = "MediaStreamLinkFilter"
;AnotherNonExistentFilter.Namespace = "..."
;AnotherNonExistentFilter.Name = "..."

;[JsFilter]
```

Der Name der Subsection (hier: <em>MediaStreamLinkFilter.</em>) kann
frei gewählt werden, die Filter werden in der Reihenfolge ausgeführt wie
sie in der Datei stehen.

Jetzt wird jedes CSS-Paket, und jede CSS-Datei welche über die Action
ausgeliefert wird zuerst nach dem definierten LinkSchema durchsucht, und
die Links wie gewünscht ersetzt.

**Wichtig:** Wenn bei einem Paket ServerCaching aktiviert ist, wird der
Filter VOR dem Cachen einmalig ausgeführt, und nicht bei jeder
Auslieferung.

# Action URLs

Im APF-Standardschema sehen die URLs für Dateien und Pakete, die
mithilfe der Action eingebunden werden wie folgt aus. **Sofern die
HtmlHeader-Nodes verwendet werden, ist es nicht notwendig dieses Schema
zu kennen, dies ist nur nötig wenn die URLs manuell eingefügt werden
müssen, da die Nodes die URL automatisch generieren.**

## Mit URL-Rewriting

``` html4strict
// Einzelne Css-Datei (themes/default/css/menu.css)
/~/extensions_htmlheader-action/JsCss/path/themes_default_css/type/css/file/menu

// Einzelnes Paket (CSS)
/~/extensions_htmlheader-action/JsCss/package/DefaultTheme.css
```

## Ohne URL-Rewriting

``` html4strict
// Einzelne Css-Datei (themes/default/css/menu.css)
/index.php?extensions_htmlheader-action:JsCss=path:themes_default_css|type:css|file:menu

// Einzelnes Paket (CSS)
/index.php?extensions_htmlheader-action:JsCss=package:DefaultTheme.css
```

# Shrinking von Java-Script-Dateien

Um Java-Script-Dateien in JS-Paketen zu verkleinern ist die externe
Bibliothek *JsMin* erforderlich. Diese ist unter
<http://code.google.com/p/jsmin-php/> verfügbar. Zur Aktivierung des
Shrinking binden Sie bitte die Datei *JsMin.php* in z.B. Ihre
Bootstrap-Datei ein um die Klasse *JsMin* im globalen Namespace
verfügbar zu machen.