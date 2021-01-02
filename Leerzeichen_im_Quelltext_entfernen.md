## Einleitung

Dieses Tutorial soll zeigen, wie an Hand von dedizierten Steuer-Zeichen
Leerzeichen und Leerzeilen aus dem Quelltext entfernt werden können.
Hintergrund der Frage unter [Leerzeichen im
Quelltext](http://forum.adventure-php-framework.org/de/viewtopic.php?f=6&t=231)
ist, dass durch strukturiert definierte APF-Templates der Form

``` html4strict
<@controller
   class="VENDOR\..\NewWikiArticlesController"
@>
<h3>Neue Wiki-Artikel:</h3>
<ul>
   <html:placeholder name="articles" />
</ul>

<html:template name="article_de">
   <li>
      <strong><template:placeholder name="name" /></strong>
      (Datum: <template:placeholder name="date" />,
      Revision: <template:placeholder name="revision" />)
   </li>
</html:template>
```

bei der Generierung der Ausgabe Leerzeichen und Leerzeilen entstehen.
Sofern die Webseite gzip komprimiert ausgeliefert wird (siehe [Apache's
mod_deflate](http://httpd.apache.org/docs/2.0/mod/mod_deflate.html)),
stellt das weiter kein Problem dar.

Sofern diese Möglichkeit nicht gegeben ist oder der Quelltext explizit
gestaltet werden möchte, so kann ein spezieller
[APF-Output-Filter](http://adventure-php-framework.org/Seite/087-Filter#3-Ausgabe-Filter)
dafür sorgen, dass die nicht erwünschten Leerzeichen und Leerzeilen
entfernt werden.

## Umsetzung

Die Idee der Umsetzung stammt aus
[JSP-Templates](http://java.sun.com/developer/technicalArticles/javaserverpages/jsp_templates/),
die im **JAVA-Bereich** für die Gestaltung von Views Einsatz finden.
Hier ist es möglich, Scriptlet-Tags (**&lt;%** bzw. **%&gt;**) dazu
einzusetzen, unerwünschte Zeichen zu entfernen.

Soll beispielsweise der Umbruch zwischen

``` html4strict
</ul>
```

und

``` html4strict
<html:template name="article_de">
```

entfernt werden, kann das per

``` html4strict
</ul><%

%><html:template name="article_de">
```

erreicht werden.

`Da dieser Ansatz nur dann greift, wenn die aufgezeigten Scriptlet-Tags auch eingesetzt werden, ist der Entwickler aufgefordert, diese überall `
`dort einzusetzen, wo Leezeichen und Leerzeilen entfernt werden sollen!`

Um dieses Verhalten auch auf die Ausgabe des APF anzuwenden, kann ein
eigener Output-Filter implementiert werden, der um diese Funktion
erweitert ist. Die folgenden Code-Boxen zeigt wie:

``` php
use APF\core\filter\ChainedContentFilter;

class ScriptletOutputFilter implements ChainedContentFilter {

   public function filter(FilterChain &$chain, $input = null) {

      // Scriptlet Tags entfernen
      $input = preg_replace('/<%([^%>]+)%>/ims','',$input);

      return $chain->filter($input);

   }

}
```

## Anwendung

Um den Output-Filter anzuwenden, muss dieser in der Bootstrap-Datei in
der Registry definiert werden. Hierzu ist folgender Code notwendig:

Hier gilt es eine kleine Änderung zu beachten, aus *addFilter* wird
*appendFilter*. Umgesetzt sieht es also wie folgt aus:

``` php
use APF\core\filter\OutputFilterChain;
use VENDOR\..\ScriptletOutputFilter;

OutputFilterChain::getInstance()->appendFilter(new ScriptletOutputFilter());
```

## Alternative Umsetzungen

Zwei Alternativen zu oben beschriebenen Verfahren, sind "radikale"
Reguläre Ausdrücke. Diese Variante hat den Vorteil, dass keine extra
&lt;% %&gt; tags gesetzt werden müssen, jedoch kann sie in Einzelfällen
auch gewünschte Formatierungen zerstören.

Beide varianten funktionieren mit obigem codebeispiel, man muss nur die
Zeile

``` php
$input = preg_replace('/<%([^%>]+)%>/ims','',$input);
```

ersetzen.

### Variante 1

Ziel dieser Variante ist, alle unnötigen Leerzeilen zu entfernen, aber
(in den meisten Fällen) den HTML-code trotzdem lesbar zu halten, indem
gewollte Zeilenumbrüche nicht entfernt werden. Diese Variante ist im
Vergleich zur Variante 2 weniger Fehleranfällig im Bezug auf zerstörte
Formatierungen.

``` php
$input = preg_replace('/((\r)?\n(\s)*){2,}/','',$input);
```

Es werden nur 2 aufeinanderfolgende Leerzeilen entfernt, wobei diese
Leerzeichen (z.b. aufgrund der Einrückung) enthalten dürfen.

### Variante 2

Diese Variante ist sehr radikal. Sie entfernt alle Leerzeilen,
Tabulatoren, und auch Leerzeichen wenn mindestens 3 hintereinander
vorkommen (um html-tags nicht zu zerstören). Übrig bleibt meist ein
einzeiliger HTML-code. Diese Variante sollte mit Bedacht benutzt werden
und es sollte gut getestet werden ob womöglich irgendwo zu viel entfernt
wird.

``` php
$input = preg_replace('/\r|\n|\t|\s{3,}/','',$input);
```

### Variante 3

Hier noch eine Möglichkeit um den Quelltext auf eine Zeile zu
reduzieren:

``` php
$input = preg_replace(
   array('/>\s+/', '/\s+</', '/[\r\n]+/'),
   array('>', '<', ' '),
   $input
);
```

### Variante 4

Eine etwas umfassendere Variante, welche jedoch berücksichtigt, dass
textarea- und pre-Inhalte nicht gefiltert werden dürfen (andernfalls
werden z.B. Formulardaten verstümmelt, sofern sie nochmal angezeigt
werden) ist Folgende: Zuerst werden die genannten Tags extrahiert, mit
einem Platzhalter ersetzt, danach der Inhalt nach einem der obigen
Filter gefiltert, und anschließend die Platzhalter wieder mit den
richtigen Tags ersetzt:

``` php
use APF\core\filter\ChainedContentFilter;

class ShrinkOutputFilter implements ChainedContentFilter {

    protected $Replacements = array();

    public function filter(FilterChain &$chain, $input = null) {
        /* Save content from textareas and <pre> element from beeing destroyed */
        $input = preg_replace_callback('/<textarea(.*)>(.*)<\/textarea>/Usi', array('ShrinkOutputFilter', 'callback'), $input);
        $input = preg_replace_callback('/<pre(.*)>(.*)<\/pre>/Usi', array('ShrinkOutputFilter', 'callback'), $input);

        $input = preg_replace(
                array('/\r|\n|\t/', '/\s{2,}/'),
                array('', ' '),
                $input
        );

        /* Re-add <textarea> and <pre> content */
        foreach($this->Replacements as $Md5 => $Html) {
            $input = str_replace('{{ShrinkOutputFilter::'.$Md5.'}}', $Html, $input);
        }

        return $chain->filter($input);
    }

    protected function callback($Hits) {
        $Md5 = md5($Hits[0] . mt_rand(0, 999999));
        $this->Replacements[$Md5] = $Hits[0];
        return '{{ShrinkOutputFilter::'.$Md5.'}}';
    }
}
```