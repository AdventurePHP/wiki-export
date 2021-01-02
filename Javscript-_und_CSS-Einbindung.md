<div style="border: 1px solid red; background: #F9F9F9; padding: 5px; font-weight: bold;">

Wichtig: Seit Version 1.15 ist diese Erweiterung sowie JsCssPackager in
die [htmlheader](/Html-Header_Erweiterung "wikilink")-Erweiterung
integriert worden. Sofern du bereits 1.15 oder aktueller nutzt gehe
bitte zur Dokumentation von
[htmlheader](/Html-Header_Erweiterung "wikilink").

</div>

Installation and configuration notes for jscssinclusion:

# Install:

You need to add a configuration file called *DEFAULT_actionconfig.ini*
in /config/extensions/jscssinclusion/biz/{YOUR CONTEXT} with the
content:

``` ini
[sGCJ]
ActionClass = "APF\extensions\jscssinclusion\biz\actions\JsCssInclusionAction"
```

Now you are ready to use it. Just generate a frontcontroller link for
the file you want to include.

=<Example:=>

Your js file is located in
*/APF/sites/example/pres/frontend/static/js/myjs.js*

Your css file is located in
*/APF/sites/example/pres/frontend/static/css/mycss.css*

### Links (without url rewriting):

*.../index.php?extensions_jscssinclusion_biz-action:sGCJ=path:sites_example_pres_frontend_static_js\|type:js\|<file:myjs>*

*.../index.php?extensions_jscssinclusion_biz-action:sGCJ=path:sites_example_pres_frontend_static_css\|type:css\|<file:mycss>*

### Links (with url rewriting):

*.../\~/extensions_jscssinclusion_biz-action/sGCJ/path/sites_example_pres_frontend_static_js/type/js/file/myjs*

*.../\~/extensions_jscssinclusion_biz-action/sGCJ/path/sites_example_pres_frontend_static_css/type/css/file/mycss*

**Parameters:**

-   path: The relative path from /APF/. You need to replace all / and ::
    with _
-   type: The type of your file. possible: js or css
-   file: The name of your file, without extension (.css, .js).

# Filtering files

## Introduction

Since APF Version 1.14 it is possible to configure own filters, which
filter the content of the files before they get send to the browser.
This makes it, for example, possible to shrink css and js files before
delivering them, or to replace static urls (e.g. background images) in
css files with more generic ones. We only provide a filter for shrinking
css files as an example filter, but you can add any filter you want.

You do not need to use the filters, if no configuration was found,
simply no filter will be used.

## Configuration

If you want to use filters, you need a configuration file in
*/config/extensions/jscssinclusion/biz/{your
context}/DEFAULT_JsCssInclusion.ini* like the one below. Js and Css
files are treated seperatly.

``` ini
[CssFilter]
ShrinkCssFilter = "APF\extensions\jscssinclusion\filter\ShrinkCssFilter"

;[JsFilter]
```

Here we only configure the ShrinkCssFilter for css files. Each filter
get's an subsection in the corresponding section. The name of the
subsection does not matter. But each subsection needs to define the
values "Namespace" and "Name", pointing to the filter you want to add.

In this example, all css files would be filtered by the
"ShrinkCssFilter", which just removes unnecessary whitespaces and
newlines, before the files are send to the browser. This minimizes the
traffic which gets transferred.

It works the same with js files, you just need to uncomment the
"JsFilter"-section and add a subsection for the filter.

## Write own filters

If you want to create your own filters, just look at the existing
filter:

``` php
class ShrinkCssFilter implements ChainedContentFilter{

    public function filter(FilterChain &$chain, $input = null) {

        $input = preg_replace('#\s+#', ' ', $input);
        $input = preg_replace('#/\*.*?\*/#s', '', $input);
        $input = str_replace('; ', ';', $input);
        $input = str_replace(': ', ':', $input);
        $input = str_replace(' {', '{', $input);
        $input = str_replace('{ ', '{', $input);
        $input = str_replace(', ', ',', $input);
        $input = str_replace('} ', '}', $input);
        $input = str_replace(';}', '}', $input);
        $input = trim($input);

        // delegate filtering to the applied chain
        return $chain->filter($input);
    }

}
```

Your filter needs to implement the "ChainedContentFilter"-interface, and
to return "$chain-&gt;filter($input);". That's all. Place it anywhere
you want in the apps folder and configure it as shown above.