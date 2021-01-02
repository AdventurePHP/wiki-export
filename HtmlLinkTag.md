Um möglichst hohe Flexibilität bei Anwendungen zu haben ist es notwendig
Links einzubauen, die gewisse Parameter modifizieren. Damit dies gleich
im Template möglich ist, wurden die folgenden Taglibs geschaffen.

# Taglibs

## dynamische Links

Der LinkGenerationTag Taglib erzeugt einen URL als String auf Basis der
übermittelten als Attribute gesetzten Parameter und dem aktuellen
LinkSheme. Das Ergebnis dieses Taglibs wird direkt ausgegeben und kann
daher zb für Pfadangaben in JavaScript verwendet werden.

Bis auf den Parameter href und queryoption, auf die in weiterer Folge
eingegangen wird, werden alle übermittelten Parameter gesetzt.

### Parameter href

Wenn das Attribut href gesetzt ist wird dieser String als Ausgangs URL
verwendet. Sollte das Attribut nicht gesetzt sein, wird der aktuelle URL
verwendet.

### Parameter queryoption

-   set = alle vorhandenen Parameter werden entfernt und die neuen
    gesetzt
-   merge = vorhandene Parameter bleiben bestehen, aber erhalten von den
    gesetzten Parametern die Werte. Weiters werden die noch nicht
    vorhandenen Parameter hinzugefügt.

## html Code eines dynamischen Linkes

Um die einfache Verwendung der Funktion zu ermöglichen gibt es auch
einen Taglib, HtmlLinkTag, der nicht nur den Link erzeugt, sondern auch
gleich den notwendige HTML a-Tag.

Um einfach sprachabhängige Texte zu ermöglichen stehen auch zwei Taglibs
zur Verfügung um auf eine Konfigurationsdatei zurückzugreifen.

### spezielle Parameter

Damit alle gängigen Attribute des HTML a-Tags möglich sind, werden
folgende Parameter 1:1 in den html Code des Tags eingebaut. Sie können
daher nicht für die zu übergebende Parameterbezeichnung verwendet
werden, da sie bei der Link generierung nicht berücksichtigt werden.

-   id
-   style
-   class
-   onabort
-   onclick
-   ondblclick
-   onmousedown
-   onmouseup
-   onmouseover
-   onmousemove
-   onmouseout
-   onkeypress
-   onkeydown
-   onkeyup
-   tabindex
-   dir
-   accesskey
-   title
-   charset
-   coords
-   href
-   hreflang
-   name
-   rel
-   rev
-   shape
-   target
-   xml:lang
-   onblur

### active class

Sobald erkannt wird, dass der Link in dem aktuellen URL enthalten ist,
sprich die Seite aktiv ist, wird die CSS-Klasse "active" zu den
definierten hinzugefügt um damit eine spezifische CSS Formatierung für
aktive links zu ermöglichen.

## sprachabhängige Link Texte

Der LinkLanguageLabelTag ermöglicht das einbinden eines sprachabhängigen
Anzeigetextes. Der Taglib wird automatisch vom HtmlLinkTag geladen so
das ein addtaglib entfällt.

``` html4strict
<a:getstring namespace="VENDOR\module" config="labels" entry="link.text" />
```

## sprachabhängige Titel Titel

Sprachabhängige "title" Texte, die als Tooltip erscheinen sobald man mit
der Maus über den Link fährt, ermöglicht der LinkLanguageTitleTag. Er
wird ebenfalls vom HtmlLinkTag automatisch geladen so das auch hier ein
addtaglib entfällt.

``` html4strict
<title:getstring namespace="VENDOR\module" config="labels" entry="link.title" />
```

## sprachabhängige Link Texte für aktive Links

Um auf aktive Links nicht nur mittels CSS reagieren zu können, gibt es
mit diesem Taglib auch die Möglichkeit einen anderen Link Text aus einer
Konfigurationsdatei zu laden. Dieser Text wird geladen, sobald der link
als active gekennzeichnet wird. Er kann auch verwendet werden, wenn kein
sprachabhängiger Link Text gesetzt wird. Dann wird einfach der gesetzte
Content des HtmlLinkTag Taglibs verwendet.

Der Taglib mit der Klasse LinkLanguageLabelActiveTag wird ebenfalls vom
HtmlLinkTag automatisch geladen so das auch hier ein addtaglib entfällt.

``` html4strict
<aActive:getstring namespace="VENDOR\module" config="labels" entry="link.text.active" />
```

## sprachabhängige Titel Titel für aktive Links

Um auf aktive Links nicht nur mittels CSS reagieren zu können, gibt es
mit diesem Taglib auch die Möglichkeit einen anderen "title" Text aus
einer Konfigurationsdatei zu laden. Dieser wird beim beim überfahren des
Linkes mit der Maus als Tooltip erscheinen. Dieser Text wird geladen,
sobald der link als active gekennzeichnet wird. Er kann auch verwendet
werden, wenn kein sprachabhängiger Titel Text gesetzt wird. Der Taglib
mit der Klasse LinkLanguageTitleActiveTag wird ebenfalls vom HtmlLinkTag
automatisch geladen so das auch hier ein addtaglib entfällt.

``` html4strict
<titleActive:getstring namespace="VENDOR\module" config="labels" entry="link.title.active" />
```

# Anwendungsbeispiel

## Beispiel 1

``` html4strict
<core:addtaglib
   class="APF\tools\link\taglib\HtmlLinkTag"
   prefix="html"
   name="a"
/>
<html:a parameter="value" newparameter="newvalue" parametertoremove="" class="cssclass">
<a:getstring namespace="VENDOR\module" config="labels" entry="link.text" />
<title:getstring namespace="VENDOR\module" config="labels" entry="link.title" />
</html:a>
```

Für das DefaultLinksheme ohne Url Rewriting und der Sprache "de" liefert
dieses folgendes, wenn der Taglib auf der Startseite eingebaut wird:

``` html4strict
<a href="domain.at?parameter=value&newparameter=newvalue" class="cssclass"
title="Produkte anzeigen">hier klicken</a>
```

### Fall Remove

Sollte der Link auf der Seite
domain.at?parameter=value&newparameter=newvalue&parametertoremove=test
(der Parameter ist parametertoremove=test) eingebaut werden, liefert er:

``` html4strict
<a href="domain.at?parameter=value&newparameter=newvalue" class="cssclass"
title="Produkte anzeigen">hier klicken</a>
```

Der oben definierte Parameter parametertoremove="" wird dabei aus dem
bestehenden URL entfernt.

### Fall Seite xyz

und falls er sich hier befindet (parameter Seite=xyz ist gesetzt)
liefert er:

``` html4strict
<a href="domain.at?Seite=xyz&parameter=value&newparameter=newvalue" class="cssclass"
title="Produkte anzeigen">hier klicken</a>
```

Der bestehende Seitenparameter bleibt also erhalten.

### Fall active class

Nachdem man den Link angeklickt hat und der selbe Taglib auf der Seite
eingebaut wird, erhält man folgende Ausgabe:

``` html4strict
<a href="domain.at?Seite=xyz&parameter=value&newparameter=newvalue" class="cssclass active"
title="Produkte anzeigen">hier klicken</a>
```

Es wurde also die class "active" hinzugefügt

### Config

``` ini
[de]
link.title="Produkte anzeigen" ;wird angezeigt sobald man mit der Maus darüber fährt
link.text="hier klicken"
[en]
link.title="Tooltip displayed when mouse is over the link"
link.text="click here"
```

## Beispiel 2 - mit unterschiedlichen Texten für aktive Links

``` html4strict
<core:addtaglib
   class="APF\tools\link\taglib\HtmlLinkTag"
   prefix="html"
   name="a"
/>
<html:a parameter="value" newparameter="newvalue" parametertoremove="" class="cssclass">
<a:getstring namespace="VENDOR\module" config="labels" entry="link.text" />
<title:getstring namespace="VENDOR\module" config="labels" entry="link.title" />
<aActive:getstring namespace="VENDOR\module" config="labels" entry="link.text.active" />
<titleActive:getstring namespace="VENDOR\module" config="labels" entry="link.title.active" />
</html:a>
```

Für das DefaultLinksheme ohne Url Rewriting und der Sprache "de" liefert
dieses folgendes, wenn der Taglib auf der Startseite eingebaut wird:

``` html4strict
<a href="domain.at?parameter=value&newparameter=newvalue" class="cssclass"
title="Produkte anzeigen">hier klicken</a>
```

### Fall Remove

selbes Ergebnis wie oben.

### Fall Seite xyz

selbes Ergebnis wie oben.

### Fall active class

Nachdem man den Link angeklickt hat und der selbe Taglib auf der Seite
eingebaut wird, erhält man folgende Ausgabe:

``` html4strict
<a href="domain.at?Seite=xyz&parameter=value&newparameter=newvalue" class="cssclass active"
title="angezeigte Produkte">Jene Produkte die soeben angezeigt werden</a>
```

Es wurde also die class "active" hinzugefügt. Weiters werden andere
Texte eingebunden.

### Config

``` ini
[de]
link.title="Produkte anzeigen" ;wird angezeigt sobald man mit der Maus darüber fährt
link.text="hier klicken"
link.title.active="angezeigte Produkte" ;wird angezeigt sobald man mit der Maus darüber fährt
link.text.active="Jene Produkte die soeben angezeigt werden"
[en]
link.title="Tooltip displayed when mouse is over the link"
link.text="click here"
link.title.active="You have already clicked this link"
link.text.active="Active link"
```