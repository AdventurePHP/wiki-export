## Einleitung

Dieses Tutorial möchte zeigen, wie das URL-Layout durch einfache
Maßnahmen für den Betrieb einer Webseite angepasst werden kann. Das APF
liefert mit der Standard-Konfiguration bereits ein
[URL-Layout](http://adventure-php-framework.org/Seite/069-URL-Rewriting)
mit, das technisch von den
[Input-Filtern](http://adventure-php-framework.org/Seite/087-Filter#Chapter-2-Eingabe-Filter)
und den mitgelieferten
[Link-Generierungs-Komponenten](http://adventure-php-framework.org/Seite/036-Links)
definiert wird.

Um die konkrete Umsetzung an einem Beispiel zeigen zu können, definieren
wir zunächst ein Layout für unsere Webseite. Zweck der Änderung ist es,
den ersten und konstanten Teile zu entfernen und den Seiten-Identifier
hinsichtlich seiner Keywords prominenter zu platzieren.

Das mitgelieferte und generisch einsetzbare Layout ist so konstruiert,
dass jeder Parameter in der URL zusammen mit seinem Wert abgebildet ist.
Hier ein Beispiel:

``` text
http://example.com/page/news
```

Dabei ist "page" der Parameter, der die anzuzeigende Seite benennt und
"news" der Name der Seite bzw. der Wert des Parameters "page". Ziel soll
sein, die URL ohne Änderung der Applikations-Logik auf die Form

``` text
http://example.com/news
```

zu reduzieren. Dabei soll es weiterhin möglich sein, zusätzliche
Parameter wie auch bisher als Key-Value-Paare der Form

``` text
/key1/value1/key2/value2
```

anzuhängen. Semantisch gesehen, soll also die URL

``` text
http://example.com/news/page/2
```

die News-Seite anzeigen, wobei sich der Inhalt selbst, die Seite 2 der
News wiederspiegelt.

## Grundlagen

Das APF liefert als Web-Entwicklungs-Framwork ein generisch einsetzbares
URL-Layout mit, das mit Hilfe von Input- und Output-Filtern angepasst
werden kann. Die Filter nehmen im Request-Konzept des APF eine
entscheidende Rolle ein. Sie dienen im Fall von Rewrite-URLs zur
Herstellung eines bekannten Umfelds (Input) und verpacken die interne
Repräsentation nach aussen so, wie es der Input-Filter verstehen wird
(Output). Dabei bereiten die Input-Filter die Request-Inhalte so auf,
wie es der PHP-Entwickler von "normalen" Request-Parametern kennt. Der
Output-Filter formatiert die URLs so, dass sie dem umgeschriebenen
Layout entsprechen.

Exakt die Entkopplung des URL-Layout - oder allgemein des
Request-Wrapper - von der eigentlichen Applikation nutzen wir in diesem
Tutorial aus um eine Änderung des Schema ohne Änderung der Software
selbst durchführen zu können.

## Umsetzung

Im Fall von Rewrite-URLs wird jeder ungerade Pfad-Abschnitt als
Parameter-Name und jeder gerade als der Wert des Vorangegangenen
gewertet. Rufen wir die URL

``` text
http://example.com/news
```

ohne Änderung auf, wäre der Parameter "page" nicht verfügbar. Da kein
weiterer folgt wird auch "news" nicht als Parameter gewertet.

Um nun sowohl den vorhandenen Input-Filtern als auch den
Link-Generierungs-Tools eine Umgebung bereitzustellen, in der sich die
Applikation wir unter der URL

``` text
http://example.com/page/news
```

verhält, müssen drei Dinge geändert werden: **URL-Rewriting**, die
PHP-Variable **$_SERVER** und die **Response-Header** im Fall von
Redirects.

### URL-Rewriting

Die Standard-RewriteRule leitet alle Anfragen auf die *index.php*
([Bootstrap-Datei](http://adventure-php-framework.org/Seite/013-Grundlagen#Chapter-3-1-Aufbau-Bootstrapdatei))
weiter und übergibt die angefragte URL an den Parameter
"apf-rewrited-query". Dieser wird im Input-Filter ausgewertet und zu
einem generischen Array aus Parameter und Werten umgewandelt. Damit der
Filter den ersten Pfad-Abschnitt auch als Wert des Parameters "page"
wertet muss die .htaccess-Datei wie folgt angepasst werden:

``` apache
RewriteRule !(\.php|\.css|\.jpe?g|\.png|\.gif|\.zip|\.rar)$ /index.php?apf-rewrited-query=/page%{REQUEST_URI}&%{QUERY_STRING} [NC,L]
```

Damit sieht der Request aus Sicht des Filters wie

``` text
http://example.com/page/news
```

aus und der Parameter "page" wird mit "news" gefüllt.

### Request-Mapping

Da PHP den umgeschriebenen Request nicht selbständig auch in die
*$_SERVER*-Variable propagiert, muss dies in der Bootstrap-Datei
manuell erledigt werden. Hierzu ist jedoch lediglich eine Code-Zeile
notwendig:

``` php
$_SERVER['REQUEST_URI'] = '/page'.$_SERVER['REQUEST_URI'];
```

Damit kann die Variable z.B. im
[RequestHandler](http://adventure-php-framework.org/Seite/096-RequestHandler)
genutzt werden, ohne dass die Applikation vom geänderten Layout Kenntnis
haben muss.

### Response-Header

Leitet eine Applikation oder ein Modul einer Seite explizit per
Location-HTTP-Header weiter und wird die URL über z.B. den LinkHandler
generiert, taucht der Parameter "page" wieder in der URL auf. Um dies zu
verhindern, kann der Output-Buffering-Mechanismus im Zusammenspiel mit
folgendem Code verwendet werden:

``` php
$headers = headers_list();
foreach($headers as $header){
   if(preg_match('/Location:/i',$header)){
      $url = trim(substr($header, strpos($header, ':') + 1));
      $url = preg_replace('~(.*)/page/(.*)~iU', '$1/$2', $url);
      header('Location: '.$url, true);
   }
}
```

Damit wird der bisherige Header durch die korrigierte URL ersetzt und es
wird dafür Sorge getragen, dass die URL auf der Ziel-Seite auch konform
zum neuen URL-Layouts ist.

Output-Buffering wird hier deshalb verwendet, da de Code Block bei einem
zuvor gesendete Header nie zur Ausführung kommt.

### Output-Filter

Um die im HTML-Quelltext ausgegebenen Links ebenfalls mit Hilfe der
vorhanden Board-Mittel (z.B. LinkHandler) generieren zu können wird ein
neuer Output-Filter benötigt, der Links mit "/page/..." zu "/..."
umschreiben. Dies gilt sowohl für Anchor-Tags als auch für
Formular-Actions. Hierzu genügt folgender Code:

``` php
use APF\core\filter\ChainedContentFilter;

class KeywordMappingOutputFilter implements ChainedContentFilter {

   public function filter(FilterChain &$chain, $input = null) {
      return preg_replace(
              '~action="(.*)/de/(.*)"~iU',
              'action="$1/$2"',
              preg_replace(
                      '~href="(.*)/de/(.*)"~iU',
                      'href="$1/$2"',
                      parent::filter($input)
              )
      );
   }
}
```

Dieser kann in der *index.php* nach dem Einbinden der *bootstrap.php*
mit einem

``` php
use APF\core\filter\OutputFilterChain;
use VENDOR\..\KeywordMappingOutputFilter;

OutputFilterChain::getInstance()->appendFilter(new KeywordMappingOutputFilter());
```

aktiviert werden.

## Zusammenfassung

Mit dem aufgezeigten Vorgehen lässt sich das URL-Layout ohne Anpassung
von Input-Filtern und Link-Generierungs-Tools bewerkstelligen. Sicher
ist das nicht die eleganteste Lösung, sie zeigt jedoch, wie das Konzept
der Ein- und Ausgabe-Filter genutzt werden kann um kleinere Anpassungen
am URL-Layout vorzunehmen.