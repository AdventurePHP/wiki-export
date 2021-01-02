<div style="border: 1px solid red; background: #F9F9F9; padding: 5px; font-weight: bold;">

Important: In APF-Version 1.15 some bigger changes were made. If you are
already working with 1.15 or higher, please visit the new documentation
at [htmlheader](/Html-Header_Erweiterung "wikilink")

</div>

Configuration notes for htmlHeader:

At first you need to place the htmlheader:gethead Taglib in your

<head>
</head>

section:

``` html4strict
<head>
  <core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="gethead" />
  <htmlheader:gethead />
</head>
```

Then you can use the other taglibs for adding javascripts, stylesheets,
titles, and meta tags to your

<head>

section wherever you want.

<div style="border: 1px solid gray; background: #F9F9F9; padding: 5px; font-weight: bold;">

Please note: using the <htmlheader:addjs /> and <htmlheader:addcss />
tags, the [jscssinclusion](/Javscript-_und_CSS-Einbindung "wikilink")
extension is needed. Please have a look at the configuration on the
linked page!

</div>

**Examples:**

``` html4strict
<!-- // Adding taglibs to your template //-->
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="addcss" />
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="addjs" />
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="addtitle" />
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="addpackage" />

<!-- // Using taglibs //-->
<htmlheader:addjs namespace="sites::example::pres::frontend::static::js" filename="jquery.min" />
<htmlheader:addcss namespace="sites::example::pres::frontend::static::css" filename="stylesheet" />
<htmlheader:addtitle append="false">This is an example title</htmlheader:addtitle>

<!-- // Using external file support, added in 1.12 //-->
<htmlheader:addjs
    url="http://static/"
    folder="js::anything"
    filename="jquery.min"
    rewriting="false"
    fcaction="false"
/>

<htmlheader:addcss
    url="http://static/"
    folder="css::anything"
    filename="stylesheet"
    rewriting="false"
    fcaction="false"
/>

<!-- // Using JsCssPackager, an extension added in 1.12 //-->
<htmlheader:addpackage
    name="form_clientvalidators_all"
    type="js"
/>
<htmlheader:addpackage
    name="mystylesheetpackage"
    type="css"
/>
```

**The parameters:**

-   url: If you want to use an external file, set the url of target
    server.
-   folder: If you use an external file, specify folder which contains
    the file.
-   namespace: The namespace of the file.
-   filename: The name of the file, without extension (e.g. '.css',
    '.js').
-   rewriting: Optional. Generate url which uses url-rewriting?
    (Default: Same as application)
-   fcaction: Optional Use fc action for delivering file? (Default:
    true)
-   append: Set append to true if you want to add the given title at the
    end of the already existing title. Otherwise set to false, if you
    want to overwrite existing titles.

Moreover, you are provided the following tags (singe 1.13):

-   html:addcsscontent
-   html:addjscontent
-   html:addstaticcss
-   html:addstaticjs

With this tags you can use external files with special parameters. For
example include the Google Maps API:

``` html4strict
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="addstaticjs" />

<htmlheader:addstaticjs
    file="http://maps.google.com/maps/api/js?sensor=false"
/>
```

<div style="border: 1px solid gray; background: #F9F9F9; padding: 5px; font-weight: bold;">

To influence the order the header content is presented within the HTML
source code, release 1.14 introduces the possibility to define the
priority. This can be done using the tag attribute <em>priority</em>.
The greater the number (int) the higher the position. Having three
elements with priorities 0 (element 1), 10 (element 2), 5 (element 3),
the output is: element 2, element 3, element 1.
Applying no <em>priority</em>, the default value is 0.

</div>

Using without other taglibs, e.g. in document controller: You need to
place the gethead taglib, but for the other taglibs you can use the
following. (And for meta-refreshs you need to use it)

``` php
// Import nodes we want to use
import('extensions::htmlheader::biz','JsNode');
import('extensions::htmlheader::biz','SimpleTitleNode');
import('extensions::htmlheader::biz','RefreshNode');
import('extensions::htmlheader::biz','CssNode');
import('extensions::htmlheader::biz','SimpleMetaNode');
import('extensions::htmlheader::biz','HttpMetaNode');
import('extensions::htmlheader::biz','PackageNode');

// Get an instance of HtmlHeaderManager:
$HHM = $this->__getServiceObject('extensions::htmlheader::biz','HtmlHeaderManager');

// Add a refresh on index.php?test=abc, with a delay of 5 seconds:
$HHM->addNode(new RefreshNode('index.php', 5, array("test" => "abc")));

// Add a title
$HHM->addNode(new SimpleTitleNode("Example title"));

// Import css-node class
import('extensions::htmlheader::biz','CssNode');

// Get instance and configure in constructor: (Before 1.12 Revision 874)
$CssNode = new CssNode($namespace, $filename);
//Get instance and configure in constructor: (After 1.12 Revision 874)
//$CssNode = new CssNode($url, $namespace, $filename, $rewriting, $fcaction);

// Add Node to HHM
$HHM->addNode($CssNode);


// Get instance and configure in constructor: (Before 1.12 Revision 874)
$JsNode = new JsNode($namespace, $filename);
// Get instance and configure in constructor: (After 1.12 Revision 874)
//$JsNode = new JsNode($url, $namespace, $filename, $rewriting, $fcaction);

// Add Node to HHM
$HHM->addNode($JsNode);

// Add a simple description meta tag
$HHM->addNode(new SimpleMetaNode('description',$description));

// Add a http meta tag definition
$HHM->addNode(new HttpMetaNode('content-type','text/html; charset=utf-8'));

// Define and add a JsCssPackager-Package to HHM
$PackageNode = new PackageNode($url, $name, $type, $rewriting);
$HHM->addPackage($PackageNode);
```

**IMPORTANT: Do not forget to import() the nodes you are using (see
example in the code above)**

<div style="border: 1px solid gray; background: #F9F9F9; padding: 5px; font-weight: bold;">

Using the PHP API of the extension (see last code sample), you can
define the priority of all node types by calling the
<em>setPriority()</em> method. Sample:

``` php
$node = new SimpleMetaNode('description',$description);
$node->setPriority(20);
$HHM->addNode($node);
```

</div>

That's all you need to know. Have fun.