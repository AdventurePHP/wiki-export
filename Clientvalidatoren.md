# Clientvalidatoren

## Funktion

Mit den Clientvalidatoren von [Ralf
Schubert](/Benutzer:Screeze "wikilink") wurde ein leistungsstarkes
Äquivalent zu den vom APF mitgelieferten Validatoren geschaffen, welches
Eingaben bereits beim Client, unter Zuhilfenahme des Javascript
Frameworks jQuery, überprüft. Bei Bedarf können auch direkt Hinweise
angezeigt werden, welche den Benutzer beim korrekten Ausfüllen
unterstützen. Diese Hinweise können bei Bedarf auch animiert angezeigt
werden, um eine besonders ansprechende Benutzeroberfläche zu erstellen.
Es sind **alle** Validatoren vorhanden, welche auch das APF
bietet.*(\*)* Sollte ein Benutzer kein Javascript aktiviert haben,
verhält sich die Anwendung wie zuvor.

*\*: Eine kleine Einschränkung gibt es: Da
**DefaultSelectFieldValidator** und **SimpleSelectControlValidator** auf
Clientseite die selbe Funktion erfüllen würden, und diese ohnehin
vorraussichtlich in 1.13 zusammengefasst werden, wurde dies bei den
Clientvalidatoren bereits in Form des **SimpleSelectValidator** getan.*

## Benötigte Dateien einbinden

Für die Validierung wird jQuery benötigt. Erstellt wurde die Extension
auf Grundlage von jQuery 1.4.2, möglicherweise ist es aber auch mit
früheren und folgenden Versionen kompatibel. jQuery kann hier
[<http://jquery.com/>](/http://jquery.com/ "wikilink") bezogen werden.

Für jeden Validator liegt unter *extensions::form::client::validator*
eine eigene *\*.js* Datei vor. Ausserdem liegt dort die
**FormValidator.js**-Datei, welche Grundvoraussetzung für die Verwendung
ist. Um die Einbindung der Dateien muss sich der Entwickler zwar selbst
kümmern, jedoch gibt es bereits praktische Extensions, welche die meiste
Arbeit abnehmen. Eine komplette Beschreibung dieser ist hier nicht
passend, deshalb nur einen kurzen Überblick, auf den jeweiligen
Dokumentationsseiten ist die ausführliche Beschreibung.

-   [JsCssPackager](/Javascript-_und_Css-Paketmanager "wikilink"): Mit
    dieser Extension ist es möglich durch einbinden einer einzigen Datei
    alle benötigten Dateien zu laden. In der mitgelieferten
    Beispielkonfiguration ist bereits ein Paket, mit sämtlichen zum
    jetzigen Zeitpunkt (1.12-beta) vorhandenen Validatoren,
    konfiguriert.
-   [JsCssInclusion](/Javscript-_und_CSS-Einbindung "wikilink"): Diese
    Extension kann einzelne Js und Css Dateien einbinden, welche sich
    außerhab des öffentlich zugänglichen Bereichs befinden.
-   [HtmlHeader](/Html-Header_Erweiterung "wikilink"): Diese Extension
    bindet die Dateien unter zuhilfenahme der beiden zuvor genannten
    Extensions ein. Besonderheit: Die Dateien können dynamisch an
    irgendeiner Template oder Codestelle eingebunden werden, und die
    generierung der Links wird automatisch erledigt.

## Verwendung

Wir gehen ab sofort davon aus, dass wir uns im Template innerhalb eines
<html:form />-Elements befinden:

``` xml
<html:form name="testForm" method="post" id="client-val">
[...]
</html:form>
```

### Einbindung der Taglibs

Vor der Verwendung müssen die einzelnen Taglibs noch dem Formular
bekannt gemacht werden:

``` xml
<form:addtaglib namespace="extensions::form::client::taglib" prefix="form" class="addclientvalidator" />
<form:addtaglib namespace="extensions::form::client::taglib" prefix="form" class="getclientvalidator" />
<form:addtaglib namespace="extensions::form::client::taglib" prefix="form" class="clientlistener" />
<form:addtaglib namespace="extensions::form::client::taglib" prefix="form" class="clienterror" />
```

### Einbinden des generierten Javascript-Codes

Hierfür ist <form:getclientvalidator /> zuständig. Aus diesem Grund muss
**am Ende des Formulars**, nach der Definition der Clientvalidatoren
**einmal** folgendes stehen:

``` xml
[...]
    <form:getclientvalidator />
</html:form>
```

### Hinzufügen von Validatoren

Die Clientvalidatoren werden genauso hinzugefügt wie die Validatoren des
APF, einziger Unterschied ist der Name der Taglib. Im Folgenden wird ein
Textfeld generiert, welches sowohl Server- als auch Clientseitig mit dem
TextLengthValidator geprüft werden soll. Das Feld erhält eine Maximal-
und Minimallänge für den Inhalt, sowie eine eigene valmarkerclass.

``` xml
<form:text
    name="text1"
    valmarkerclass="my-form-error"
    minlength="5"
    maxlength="10"
/>
<form:addvalidator
    class="TextLengthValidator"
    button="submit"
    control="text1"
/>
<form:addclientvalidator
    class="TextLengthValidator"
    button="submit"
    control="text1"
    onblur="true"
/>
```

Die Addclientvalidator-Taglib akzeptiert 2 zusätzliche, optionale
Attribute:

-   onblur: Clientseitig wird standartmäßig das Formular nach Klick auf
    den Absendebutton ausgewertet, und im Falle einer falschen Angabe
    dann das Absenden verhindert. Bei Bedarf kann ein Control aber auch
    sofort, wenn es den Fokus verliert, geprüft werden. (Das ist
    normalerweise der Fall, wenn der Benutzer fertig mit der Eingabe in
    dieses Feld ist) Beim Absenden findet dann nochmal eine
    Gesamtprüfung statt.
-   namespace: Wenn dieses Attribut nicht angegeben wird, wird unter dem
    Namespace *extensions::form::client::validator* nach dem Validator
    gesucht. Sollte sich der Validator an einer anderen Stelle befinden,
    muss dessen Namespace angegeben werden.

Es ist natürlich, genau wie bei <form:addvalidator />, möglich einen
Validator auf mehrere Felder zu setzen, indem die Feldnamen durch \|
getrennt werden.

### Anzeigen von Fehlern

#### Formularfehler

Der Formularfehler wird angezeigt, wenn das gesamte Formular geprüft
wird, und mindestens ein Feld nicht valide ist. Er verhält sich
äquivalent zu <form:error />

``` xml
<form:error>
    <div class="error">
      (error taglib): Formular nicht valide
    </div>
</form:error>
<form:clienterror>
    <div class="error">
      (clienterror taglib):Formular nicht valide
    </div>
</form:clienterror>
```

Damit der Fehler standartmäßig unsichtbar ist, wird folgende
css-Definition benötigt:

``` css
.apf-form-clienterror{
    display:none;
}
```

#### Listener

Ein Listener wird auf ein bestimmtes Feld gehängt und benachrichtigt,
wenn die Prüfung dieses Feldes eine invalide Eingabe ergeben hat.

``` xml
<form:clientlistener
    control="text1"
    animationproperties="{ opacity: 0.25, left: '+=50', height: 'toggle' }"
    animationoptions="{'duration':150}"
>
    Dieses Feld erwartet mindestens 5 und maximal 10 Buchstaben!
</form:clientlistener>
```

**Attribute:**

-   control: Das Feld auf welches der Listener hören soll.
-   animationproperties (optional): Wenn eine Animation zum Anzeigen
    verwendet werden soll, kann diese hier konfiguriert werden.
-   animationoptions (optional): Ebenfalls optionen zum Anzeigen einer
    möglichen Animation.

**Für eine Animierung wird jQuery's Funktion .animate() verwendet,
Informationen über die Verwendung gibt es hier
[<http://api.jquery.com/animate/>](/http://api.jquery.com/animate/ "wikilink")**

Wenn keine Animation verwendet wird, wird nur die valmarkerclass
hinzugefügt. Die standart APF-Valmarkerclass ist *.apf-form-error*.

Damit der Listener standartmäßig unsichtbar ist, wird folgende
css-Definition benötigt:

``` css
.apf-form-clientlistener{
    display:none;
}
```

**Hinweis für das Dynamische Hinzufügen von Listenern zu einem
Formular:**

Im Forum wird unter
[<http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=593>](/http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=593 "wikilink")
eine Möglichkeit beschrieben, wie man Listener dynamisch aus einem
Controller zu einem Formular hinzufügen kann.