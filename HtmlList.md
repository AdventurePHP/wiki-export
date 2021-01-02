HTMLList is a taglib-class to easily create dynamically lists in the
format html. You can build up three types of lists: unordered, ordered
and definition. So you can add more lists in succession and get them via
an identifier. In an ordered/unordered list, you can add simple
elements; in a definition list you have to - to keep in html standard -
add a defintion term first followed by a definition itself. The way to
do that is always the same, just look through the interfaces. An example
gives a first way through.

## Installation

Unpack the zipped file provided under [List
Taglib](http://forum.adventure-php-framework.org/de/viewtopic.php?f=11&t=315)
into 'extensions/' of the APF or checkout the php5/1.12 branch from the
[SVN](/Struktur_des_APF-SVN_Repository "wikilink").

## Interfaces

-   List:
    -   html_taglib_list::addList( {type:string} , {attributes:array}
        );
    -   html_taglib_list::getListById( {id:string} );
-   Ordered/Unordered-List:
    -   list_taglib_ordered::addElement( {<content:string>} ,
        {cssClass:string} );
    -   list_taglib_unordered::addElement( {<content:string>} ,
        {cssClass:string} );
-   Definition-List:
    -   list_taglib_definition::addDefinitionTerm( {<content:string>}
        , {cssClass:string} );
    -   list_taglib_definition::addDefinition( {<content:string>} ,
        {cssClass:string} );

## Example

``` php
use APF\extensions\htmllist\taglib\HtmlListTag;
use APF\extensions\htmllist\taglib\UnorderedListTag;

$listTag = new HtmlListTag();
$listTag->addList('list:unordered', ['id' => 'rsslist']);

/* @var $list UnorderedListTag */
$list = $listTag->getListById('rsslist');

// $lstFeeds is an array with a certain size
$feed = [];
foreach ($feed as $item) {
   $list->addElement($item, 'list_css_class');
}

echo $list->transform();
```