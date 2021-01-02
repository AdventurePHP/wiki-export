## Einleitung

Aus dem Thread [Eigene Framework vs. bestehende
Frameworks](http://www.php.de/software-design/77134-eigene-framework-vs-bestehende-frameworks.html)
auf php.de entstand die Idee eines Tutorials zu eigenen URL-Layouts
basierend auf dem APF. Diskussionsgegenstand waren flexible URL-Layouts,
die beliebige Aussen-Repräsentationen in eine interne Repräsentation
überführen und damit für beliebige Elemente einer Software ohne
explizite Kenntnis der Semantik der URL verfügbar zu machen.

Die geforderte Flexibilität kann mit dem APF basierend auf Input-Filtern
erreicht werden. Diese bieten die Möglichkeit vor und nach der
Ausführung der des Front-Controllers (nutzbar ab 1.14-SVN und Commit
\#1285) Filter zu injizieren, die das URL-Layout hin und zurück auflösen
und so die Anwendung selbst von der externen Repräsentation unberührt
bleibt.

Als Basis für die Implementierung wird die neue Filter-Implementierung
des APF genutzt, die bereits in 1.13 eingeführt und in 1.14 komplett
eingeführt wurde. Diese bietet die Möglichkeit, die vom Front-Controller
genutzte *InputFilterChain* und *OutputFilterChain* entsprechend unseren
Anforderungen zu konfigurieren.

Detail-Informationen zu den APF-Filtern lassen sich dem Kapitel
[Filter](http://adventure-php-framework.org/Seite/137-Filter) der
Dokumentation entnehmen.

## URL-Schema

Das diskutierte URL-Schema besteht aus einem quasi-festen Teil, der als
Adressierung einer dedizierten (CMS-)Seite genutzt wird und einem
variablen Anteil, der eine generische Anzahl von Parametern und
Front-Controller-Anweisungen aufnehmen kann.

### Einfache URLs

``` text
http://www.example.com/news
http://www.example.com/categories/food
```

### Einfache URLs mit einfachen Parametern

``` text
http://www.example.com/news/~/page/4
http://www.example.com/categories/food/~/page/10/sort-by/date
```

### URLs mit Front-Controller-Anweisungen

``` text
http://www.example.com/~/pages_example_com-action/ajax-api/command/load-news/id/123
http://www.example.com/~/pages_example_com-action/dynamic-images/namespace/pages_example_com/file/news_icon
```

### Mischformen

``` text
http://www.example.com/news/~/page/4/~/pages_example_com-action/ajax-api/command/load-news/id/123
http://www.example.com/categories/food/~/page/10/sort-by/date/~/pages_example_com-action/search/search-term/pasta
```

## Idee der technischen Umsetzung

Die Abbildung der obigen Eingabe-URLs auf eine interne Repräsentation
sieht vor, dass der URL-Abschnitt bis zum Trennzeichen "/\~/" als Wert
der zu ladenden Seite gewertet wird und alle anderen Abschnitte als
dynamische Parameter in einer statischen Ausprägung von
Parameter-Wert-Zuordnungen bzw. einer dynamischen Ausprägung mit
Front-Controller-Anweisungen. Die genannten Bereiche sind jeweils mit
dem genannten Trennzeichen beschnitten. Eine kombinierte URL (natürlich
inhaltlich unsinnig) hat dann folgende Gestalt:

``` text
http://www.example.com/news/~/page/4/~/pages_example_com-action/ajax-api/command/load-news/id/123/~/sort-by/date
```

Der Inhalt des ersten Pfad-Abschnitts soll in den Request-Parameter
**content-ident** gespeichert werden.

## Realisierung

### Basis-Implementierung

Der Eingabe-Filter (a.k.a. InputFilter) hat nun die Aufgabe, den im
vorangegangenen Kapitel in Prosa beschriebenen Fall aufzulösen. Hierzu
ist folgendes Grundgerüst notwendig:

``` php
class CmsUrlLayoutResolvingInputFilter implements ChainedContentFilter {
   public function filter(FilterChain &$chain, $input = null) {
      return $chain->filter($input);
   }
}
```

Der obige Code-Block zeigt einen Filter, der quasi transparent den Input
des vorangegangenen durchreicht und selbst keine Funktionalität
ausführt.

Um die Funktionalität implementieren zu können ist noch wichtig zu
wissen, wie Rewrite-URLs im APF behandelt werden. Das Routing der
Anfragen auf eine einzige Bootstrap-Datei wird über eine generische
Apache-RewriteRule gelöst, die den Request-Pfad in eine Request-Variable
schreibt und an die Bootstrap-Datei übergibt:

``` apache
RewriteEngine on
RewriteRule !(\.php|\.css|\.jpe?g|\.png|\.gif|\.zip|\.rar)$ /index.php?apf-rewritten-query=%{REQUEST_URI}&%{QUERY_STRING} [NC,L]
```

Damit ist der relevante Pfad in

``` php
$_REQUEST['apf-rewritten-query']
```

zur weiteren Verarbeitung verfügbar. Diese Vorgehensweise wurde zwar als
Standard für das APF definiert, kann aber durch die Implementierung
eines eigenen Input-Filters auch auf andere Weise gehandhabt werden.

### Implementierung des Eingabe-Filter

Der Eingabe-Filter benötigt zur Verarbeitung des genannten Layouts
folgenden Code:

``` php
class CmsUrlLayoutResolvingInputFilter implements ChainedContentFilter {

   /**
    * @var string Url identifier for the content page to display.
    */
   public static $CONTENT_IDENTIFIER = 'content-ident';
   private static $REWRITE_QUERY_PARAM = 'apf-rewritten-query';
   private static $PARTS_SEPARATOR = '/~/';
   private static $FRONT_CONTROLLER_ACTION_IDENTIFIER = '-action';

   public function filter(FilterChain &$chain, $input = null) {

      // remap request url to special syntax due to link generation that is not able to
      // handle this at the moment (for this reason, link schemes have to be introduced!)
      $_SERVER['REQUEST_URI'] = '/' . self::$CONTENT_IDENTIFIER . $_SERVER['REQUEST_URI'];

      $query = $_REQUEST[self::$REWRITE_QUERY_PARAM];
      //echo printObject($query);

      // split url by parts to separate first part from dynamic params
      // or front controller instructions
      $parts = explode(self::$PARTS_SEPARATOR, $query);
      //echo printObject($parts);

      // populate page identifier
      if (isset($parts[0])) {
         $_REQUEST[self::$CONTENT_IDENTIFIER] = $parts[0];
      }

      // resolve further parts
      $count = count($parts);
      if ($count > 1) {
         for ($i = 1; $i < $count; $i++) {
            if (strpos($parts[$i], self::$FRONT_CONTROLLER_ACTION_IDENTIFIER) !== false) {
               $this->resolveFrontControllerParams($parts[$i]);
            } else {
               $_REQUEST = array_merge($_REQUEST, $this->resolveSimpleParams($parts[$i]));
            }
         }
      }

      return $chain->filter($input);
   }

   private function resolveSimpleParams($input) {
      $parts = explode('/', $input);
      $params = array();

      $x = 0;

      // walk throug the new request array and combine the key (offset x) and
      // the value (offset x + 1)
      while ($x <= (count($parts) - 1)) {

         if (isset($parts[$x + 1])) {
            $params[$parts[$x]] = $parts[$x + 1];
         }

         // increment offset with two, because the next offset is the key
         $x = $x + 2;
      }


      return $params;
   }

   private function resolveFrontControllerParams($input) {
      $parts = explode('/', $input);

      $namespace = str_replace('_', '::',
                      str_replace(self::$FRONT_CONTROLLER_ACTION_IDENTIFIER, '', $parts[0]));
      $action = $parts[1];

      // resolve params
      $actionParams = array();

      if (count($parts) > 2) {

         $parts = array_slice($parts, 2);
         $count = count($parts);

         if ($count > 0) {
            $x = 0;
            while ($x <= ($count - 1)) {
               if (isset($parts[$x + 1])) {
                  $actionParams[$parts[$x]] = $parts[$x + 1];
               }
               $x = $x + 2; // increase by two, because next offset is the value!
            }
         }
      }

      $fC = &Singleton::getInstance('Frontcontroller');
      /* @var $fC Frontcontroller */
      $fC->addAction($namespace, $action, $actionParams);
   }

}
```

Werden die auskommentierten Zeilen mit dem Aufruf der Funktion
*printObject()* wieder in den Quellcode aufgenommen ergibt sich folgende
Ausgabe für die URL **/Startseite/\~/page/2/foo/bar/**:

``` text
/Startseite/~/page/2/foo/bar/

Array
(
    [0] => /Startseite
    [1] => page/2/foo/bar/
)

Array
(
    [apf-rewrited-query] => /de/Startseite/~/page/2/foo/bar/
    [content-ident] => /Startseite
    [page] => 2
    [foo] => bar
)
```

### Implementierung eines Link-Schemes

Sofern innerhalb der Applikation auschließlich eigene Komponenten
verwendet werden, besteht keine Notwendigkeit, für die Link-Generierung
eine generische Funktionalität zur Verfügung zu stellen. Werden
bestehende Anwendungen genutzt, so ist es wie im Kapitel
[Links](http://adventure-php-framework.org/Seite/138-Links) beschrieben
ratsam ein eigenes Link-Schema zu implementieren.

Ein solches Schema repräsentiert eine URL auf Basis einer
allgemeingültigen URL-Repräsentation (Klasse: *Url*). Arbeiten alle
bestehenden Module und Komponenten ebenfalls konsequent auf der
abstrakten URL, so kann das URL-Layout einfach durch Austauschen eines
Link-Schema angepasst werden.

Der folgende Code-Block zeigt die Implementierung des Link-Schemes, das
das Layout des Input-Filters für bestehende und neue Komponenten
erzeugen kann:

``` php
class CmsUrlLayoutLinkScheme extends BasicLinkScheme implements LinkScheme {
   const REWRITE_PARAM_TO_ACTION_DELIMITER = '/~/';

   public function formatLink(Url $url) {

      // get query and extract content ident
      $contentIdent = $url->getQueryParameter(CmsUrlLayoutResolvingInputFilter::$CONTENT_IDENTIFIER);

      // append the first part
      $path = $this->getFormattedBaseUrl($url);
      if ($contentIdent !== null) {
         $path .= '/' . $contentIdent;
      }

      // append further params
      $params = $url->getQuery();
      unset($params[CmsUrlLayoutResolvingInputFilter::$CONTENT_IDENTIFIER]);
      $path .= count($params) > 0 ? self::REWRITE_PARAM_TO_ACTION_DELIMITER : '';

      foreach ($params as $key => $value) {
         $path .= $key . '/' . $value . '/';
      }

      // add fc actions
      $actions = $this->getActionsUrlRepresentation(Registry::retrieve('APF\core', 'URLRewriting', false));
      if (!empty($actions)) {
         $path .= self::REWRITE_PARAM_TO_ACTION_DELIMITER . $actions;
      }

      return $path;
   }

   public function formatActionLink(Url $url, $namespace, $name, array $params = array()) {
      // ... not part of this tutorial ...
      return $this->formatLink($url);
   }

}
```

/\* ... to be continued ... \*/