## Einleitung

Mittels der ForwardMessage-Erweiterung ist es möglich,
(Status-)Nachrichten auf der nachfolgenden Seite anzuzeigen. Ein
Anwendungsfall wäre beispielsweise ein Anmeldevorgang: Ist die Anmeldung
erfolgreich und soll der Benutzer darüber benachricht werden, geschieht
das in der Regel auf einer nachfolgenden Seite.

Die Erweiterung wurde in folgendem Thema vorgeschlagen:
<http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=393>

## Verwendung

**Template \#1:**

Die Nachrichten werden im Template definiert (können aber auch im
Controller hinzugefügt werden). Zunächst muss die benötigte Taglib
hinzugefügt werden:

``` html4strict
<!--
   Bis einschließlich Release 1.16
-->
<core:addtaglib
   namespace="extensions::forwardmessage::pres::taglib"
   prefix="html"
   class="addforwardmessage"
/>

<!--
   Ab Release 1.17
-->
<core:addtaglib
   namespace="extensions::forwardmessage::pres::taglib"
   class="AddForwardMessageTag"
   prefix="html"
   name="addforwardmessage"
/>
```

Nun wird unter Verwendung der Taglib eine Nachricht hinzugefügt:

``` html4strict
<html:addforwardmessage name="successfully_moved" [show="true"]>
   <div class="success">Die Kategorie wurde erfolgreich verschoben.</div>
</html:addforwardmessage>
```

Über das Attribut *name* kann die Nachricht im Controller angesprochen
werden, um die Meldung je nach Erfolg der Operation anzuzeigen oder
auszublenden. Standartgemäß werden alle Nachrichten ausgeblendet. Um
dieses Verhalten direkt im Template zu verändern, existiert der
optionale Parameter *show*. Enthält dieser den Wert "true", wird die
Nachricht ohne weitere Modifikationen auf der folgenden Seite
eingeblendet.

**Controller \#1:**

Um den Status der Nachricht (einblenden\|verstecken) im Controller zu
beeinflussen, wird zunächst eine Instanz des Managers benötigt:

``` php
$forwardMessageMgr = &$this->getServiceObject(
   'extensions::forwardmessage::biz',
   'ForwardMessageManager',
   APFService::SERVICE_TYPE_SESSION_SINGLETON
);
```

Die Nachricht kann dann mittels *showMessage()* eingeblendet bzw.
mittels *hideMessage()* ausgeblendet werden:

``` php
$forwardMessageMgr->showMessage('successfully_moved'); // Zeigt die Nachricht
$forwardMessageMgr->hideMessage('successfully_moved'); // Versteckt die Nachricht
```

**Template \#2:**

Um die Nachrichten im Zieltemplate (= Template der folgenden Seite)
anzuzeigen, wird der Tag *html:getforwardmessages* benötigt:

``` html4strict
<!--
   Bis einschließlich Release 1.16
-->
<core:addtaglib
   namespace="extensions::forwardmessage::pres::taglib"
   prefix="html"
   class="getforwardmessages"
/>

<!--
   Ab Release 1.17
-->
<core:addtaglib
   namespace="extensions::forwardmessage::pres::taglib"
   class="DisplayForwardMessagesTag"
   prefix="html"
   name="getforwardmessages"
/>
```

Einbindung der Nachrichten an der entsprechenden Stelle:

``` html4strict
<html:getforwardmessages />
```

## Erweiterung

Die oben beschriebene Funktionalität wurde durch eine Idee von Jens
Prangenberg um die Möglichkeit erweitert, innerhalb des
*<html:addforwardmessage />* mit dem Tag *<message:getstring />* (analog
zu
[<html:getstring />](http://adventure-php-framework.org/Seite/046-Standard-TagLibs#Chapter-2-2-Getstring))
sprachabhängige Texte einfügen zu können.

Die Anwendung im Template funktioniert wie folgt:

``` html4strict
<!--
   Bis einschließlich Release 1.16
-->
<core:addtaglib
   namespace="extensions::forwardmessage::pres::taglib"
   prefix="html"
   class="addforwardmessage"
/>

<!--
   Ab Release 1.17
-->
<core:addtaglib
   namespace="extensions::forwardmessage::pres::taglib"
   class="AddForwardMessageTag"
   prefix="html"
   name="addforwardmessage"
/>

<!--
   Für beide Releases
-->
<html:addforwardmessage name="successfully_moved" [show="true"]>
   <div class="success">
      <message:getstring namespace="" config="" entry="" />
   </div>
</html:addforwardmessage>
```

Der Quellcode ist ab dem SVN-Commit **\#1103** und dem Release 1.13
verfügbar.