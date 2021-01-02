## Beschreibung

Wie unter [Refactoring
Form-Taglibs](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=168&start=15)
und [Frage zu Formularen und
core:appendnode](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=264)
diskutiert, soll im nächsten Release die Formular-Validierung nochmals
erweitert werden.

Ziel ist es, dass der Entwickler mit Hilfe der mitgelieferten
Validatoren einfacher spezifizieren kann, wie ein invalides Feld
markiert werden soll. Hierfür sind folgende Varianten vorgesehen:

### Standard-Markierung

Im Standard-Anwendunsfall sollen als fehlerhaft markierte
Formular-Elemente mit der CSS-Klasse **apf-form-error** belegt werden.
Diese können mit dafür definierten CSS-Klassen für den entsprechenden
Anwendungsfall formatiert werden. Beispiel:

``` css
#formId input.apf-form-error {
   border: 2px solid red;
}
```

### Eigene Markierung

Durch die Definition des Attributs **valmarkerclass** kann die im
letzten Kapitel beschriebene Standard-Markierung beeinflusst werden. Das
Attribut wird vom Validator genutzt um die CSS-Klasse festzulegen. Ist
das Attribut im zu validierenden Textfeld definiert, wird der Wert statt
dem Standard-Wert zur Markierung genutzt. Beispiel:

``` html4strict
<form:text name="foo" valmarkerclass="my-form-error" />
<form:addvalidator
   class="TextLengthValidator"
   button="..."
   control="foo"
/>
```

### Markierung als Inline-Style

Die Markierung von invaliden Formular-Feldern soll zukünftig nicht mehr
angeboten werden. Grund hierfür ist de Vermeidung von *inline styles*,
die als eine der Maßnahmen bei der Optimierung von Webseiten angeführt
wird.

## Verfügbarkeit

Die neue Funktion ist ab dem Commit 840 im SVN-Branch
[php5/1.12](http://adventurephpfra.svn.sourceforge.net/viewvc/adventurephpfra/branches/php5/1.12/)
verfügbar.