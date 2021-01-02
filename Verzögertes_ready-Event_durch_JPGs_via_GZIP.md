## Einleitung

Ich habe in den letzten beiden Tagen einem Phenomen hinterher gejagt,
das ich tatsächlich bisher noch nicht gesehen habe. Es lässt sich in
diversen aktuellen Browsern reproduzieren (FF 3.6.X, Safari 4, ...) und
da ich nach längerem Nachdenken die Lösung auch verstanden habe, wollte
ich euch natürlich teilhaben lassen.

## Anwendungsfall

Ich streame zur einfacheren Auslieferung von Modulen Bilder, die zu
einem Modul gehören über eine Front-Controller-Action. Das hat den
Vorteil, dass alle Bilder in einem Zip-Package zusammen mit dem Code
ausgeliefert werden können und keine umständliche Installation von Code
und Medien erfolgen muss. Die Action wird innerhalb einer
Bootstrap-Datei ausgeführt, die den darin enthaltenen Code durch

``` php
ob_start('ob_gzhandler');
...
ob_end_flush();
```

einschließt. Die Action sendet die entsprechenden Header und streamt das
Bild (oder auch CSS, JS, ...). Die Header sehen in exakt so aus:

``` text
Date    Wed, 13 Oct 2010 20:48:38 GMT
Server    Apache/2.0.63 (Win32) PHP/5.2.14
X-Powered-By    PHP/5.2.14
Content-Length    88285
Cache-Control    public; max-age=604800
Last-Modified    Wed, 13 Oct 2010 22:48:38 GMT
Expires    Wed, 20 Oct 2010 22:48:38 GMT
Content-Encoding    gzip
Vary    Accept-Encoding
Keep-Alive    timeout=15, max=100
Connection    Keep-Alive
Content-Type    image/jpg
```

Die Image-URL ist

``` text
http://***/Bildergalerie/***/~/3rdparty_imageresizer-action/showImage/image/advent08_1286992853/ext/jpg/path/GalleryMediaPath
```

## Auswirkung

Bei der Anzeige der Bildergalerie wird die Seite mit seinen Inhalten
schnell geladen. Das Bild, das über die FC-Action ausgeliefert wird,
ebenfalls, jedoch dauert es dann im Durchschnitt 15s bis in der Seite
das "document ready"-Event ausgelöst wird und der Browser signalisiert,
die Seite sei komplett geladen.

## Lösungsversuche

Setze ich statt JPG auf PNG, wird die Seite tadellos schnell geladen.
Deaktiviere ich GZIP encoding, ist das "Problem" genauso schnell
verschwunden, wie bei einem "Connection: close"-Header. Ich dachte
zuerst noch daran, dass die per strlen() berechnete "Content-Length"
falsch wäre, jedoch stimmt diese absolut mit der Datei-Größe überein.

## Lösung

Problem bei der Auslieferung mit GZIP-Encoding und JPGs ist, dass JPGs
fast nicht komprimierbar sind. Das bedeutet, dass der Browser das Bild
schon vollständig innerhalb einer geringeren Daten-Menge erhält und
damit wartet, dass die restlichen Daten "auch noch" ankommen. Ein
eindeutiges Indiz dafür ist, dass ein "Connection: close"-Header dem
Browser in der Lage ist mitzuteilen, dass der Datenstrom nun zu Ende ist
(Holzhammer-Methode). Aus diesem Grund sollte man tunlichst keine
"Content-Length"-Header senden, wenn man nicht tatsächlich weiß, welche
Content-Länge ein gezipptes Objekt hat.