## Frage

Können Templates und Formulare direkt (=ohne Einsetzen in einen
Platzhalter) im Document-Controller ausgegeben werden.

## Antwort:

Ja. Die Formular- und Template-Taglib unterstützen das
"TransformOnPlace"-Feature. Das bedeutet, dass zur Ausgabe kein weiterer
Platzhalter benötigt wird, sondern einfach im Document-Controller das
jeweilige Objekt angewiesen werden kann, sich selbst darzustellen:

``` php
// Formular direkt ausgeben
$form = $this->__getForm('formular');
$form->transformOnPlace();

// Template direkt ausgeben
$tmpl = $this->__getTemplate('test2');
$tmpl->transformOnPlace();
```

Ist es gewünscht, das Template in das Formular einzusetzen und das
Formular direkt auszugeben, so kann innerhalb des <html:form />-Tags ein
<form:placeholder /> definiert werden und dort das Template eingesetzt
werden:

#### Template:

``` html4strict
<html:form name="formular" method="post">
   <form:placeholder name="test2" />
   <form:text name="text" />
   <form:button name="senden" />
</html:form>
<html:template name="test2">
   <p>text angekommen: <strong><template:placeholder name="name" /></strong></p>
</html:template>
```

#### Controller:

``` php
// Formular beziehen
$form = $this->__getForm('formular');

// Platzhalter füllen
if(...){
   $tmpl = $this->__getTemplate('test2');
   $form->setPlaceHolder('test2',$tmpl->transformTemplate());
}

// Formular direkt ausgeben
$form->transformOnPlace();
```

Mit der Version 1.11 kannst du für die Validierung von Formularen auch
*<form:error />* und *<form:listener />* Tags nutzen um
"Platzhalter"-Bereich im Formural zu definieren. Hierzu wird jedoch die
aktuelle [SVN-Version](/Struktur_des_APF-SVN_Repository "wikilink")
(1.11), die Anfang 2010 als stable erscheinen wird. Weitere
Dokumentation dazu findet sich unter
[1](http://adventure-php-framework.org/Seite/113-Formulare#3-18-Listener).