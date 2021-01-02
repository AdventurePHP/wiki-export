In diesem Tutorial setzen wir am Punkt ["Hello
World"](http://adventure-php-framework.org/Seite/111-Download-Installation-und-erste-Schritte)
an und betrachten das APF etwas tiefgründiger. Am Ende steht uns ein
Registrierungsformular mit Login zur Verfügung. Als kleinen Bonus muss
die Registrierung durch das Anklicken eines Bestätigungslinks bestätigt
werden.

## Einleitung

Nachdem wir im Tutorial "Download, Installation und erste Schritte"
unsere Ordnerstruktur sowie ein "Hallo Welt! Hello World!" auf dem
Bildschirm gezaubert haben, wollen wir nun etwas tiefer in die Materie
Adventure PHP Framework (APF) einsteigen.

## Ausgangszustand

Wir haben unsere Ordnerstruktur wie im vorherigen Tutorial angelegt,
eine Template-Datei namens "helloworld.html" sowie eine Bootstrap-Datei
namens "index.php" angelegt. Um vom gleichen Stand ausgehen zu können,
kann hier das vorgefertigte Paket herunter geladen werden:
[Media:Tutorial_start.zip ](/Media:Tutorial_start.zip "wikilink")

Ich möchte darauf hinweisen, dass dieses Tutorial auf die APF-Version
1.13 stable ausgelegt ist. Aufgrund meiner Online-Server-Konfiguration
liegt mein Apps-Ordner innerhalb des Root-Verzeichnisses. Dieses ist
also mit .htaccess gegen fremden Zugriff gesichert [(Kapitel
4.2)](http://adventure-php-framework.org/Seite/111-Download-Installation-und-erste-Schritte#Chapter-4-2-Installation-innerhalb-Root-Verzeichnis)

## Voraussetzungen

Wir möchten unsere Hallo Welt!-Webseite nun um einen Navigations- und
Inhaltsbereich erweitern, sowie ein Login- und Registrierungsformular
einbinden. Der Login und die Registrierung basiert auf dem
Usermanagement-Modul, kurz UMGT. Die Registrierung muss anhand eines
Bestätigungsschlüssels bestätigt und das Konto somit aktiviert werden.

Ausserdem werden wir diverse Tools und Extensions kennen lernen und
diese für unser Projekt nutzen.

## Template-Struktur anlegen

Unser Ziel ist es, im oberen Bereich unserer Seite eine Navigation
einzubinden und im Bereich darunter die passenden Inhalte darzustellen.
Um die Templates für Navigation und Inhalt besser trennen zu können,
verwalten wir diese in zwei Unterordnern "menu" und "content".

Folgendermassen erweitern wir unsere Ordnerstruktur:

``` dos
sites/
    testwebsite/
        pres/
            templates/
                menu/
                content/
```

Wir erstellen nun zwei Templates im jeweiligen Unterordner: "menu.html"
und "content.html", sodass unsere Ordnerstruktur mit Templates am Ende
so aussieht:

``` dos
sites/
    testwebsite/
        pres/
            templates/
                helloworld.html
                menu/
                    menu.html
                content/
                    content.html
```

Nun müssen wir unsere "helloworld.html" editieren, um die Navigation
sowie die Inhalte dort mit einzubinden. Warum "helloworld.html"? Weil
diese über unsere Bootstrap-Datei "index.php" via

``` php
$page->loadDesign('sites\helloworld\pres\templates','helloworld');
```

geladen wird.

Unsere "helloworld.html" soll dann in etwa so aussehen:

``` html4strict
<core:importdesign namespace="sites\helloworld\pres\templates\menu" template="menu" />
<core:importdesign namespace="sites\helloworld\pres\templates\content" template="content" />
```

Wir halten also fest: Sämtliche Änderungen, die wir an der
"helloworld.html" vornehmen, wirken sich auch auf alle anderen, noch
folgenden Seiten aus. Es ist somit also auch folgerichtig von Vorteil,
den Grossteil des Designs bzw. den Aufbau der Seite dort vorzunehmen und
festzulegen.

Kommen wir zur Erklärung, was dieses nun macht. Dies ist eine spezielle
TagLib, welche das APF standardmässig zur Verfügung stellt. Und anhand
der speziellen TagLib

``` html4strict
importdesign
```

können wir, wie der Name sagt, Designs, also andere Elemente
importieren. Das APF stellt noch viele weitere TagLibs zur Verfügung,
darauf kommen wir im Verlauf des Tutorials noch zu sprechen. Ausserdem
müssen wir bald dieses Tag noch anpassen, also nicht vergessen!

Rufen wir nun unsere Seite im Browser auf, sehen wir nichts. Obwohl,
nichts ist ja nicht richtig. Wir sehen eine weisse Seite, wenn alles
richtig verlaufen ist. Damit wir nun aber genau feststellen können, dass
wirklich unsere "menu.html" und "content.html" eingebunden wurde,
bearbeiten wir diese beiden Dateien und schreiben ihre Namen hinein. Die
"menu.html" sollte so aussehen:

``` html4strict
menu.html
```

und die "content.htm" so:

``` html4strict
content.html
```

Rufen wir nun erneut unsere Webseite auf, sollte auf dem Monitor eine
Ausgabe wie folgt erscheinen:

``` html4strict
menu.html content.html
```

Das sieht soweit doch schon recht vielversprechend aus. Nun können wir
beginnen, eine kleine CSS-Datei zu erarbeiten, damit unsere Inhalte
anschliessend etwas schöner und strukturierter dargestellt werden.

Die Beschreibung und der Aufbau der CSS soll hier keine Rolle spielen
und wird auch nur nebensächlich erwähnt. Es gibt an anderer Stelle
deutlich bessere Referenzen bezüglich CSS als ich sie hier vermitteln
könnte.

## Ein einfaches Style-Sheet

Ein einfaches Style-Sheet, welches für dieses Tutorial völlig
ausreichend ist, möchte ich nun vorstellen. So sieht es im ganzen aus:

``` css
#header {
   background-color: #d5fcf4;
   padding: 0;
   border: 1px solid #0c9;
   margin: 10px;
}

#main {
   background-color: #d5eeff;
   padding: 0;
   border: 1px solid #09f;
   margin: 10px;
   position: relative;
}
```

Um dies nun für unsere Webseite verwenden zu können, müssen wir unsere
"helloworld.html" noch weiter bearbeiten. Wir müssen einen ordentlichen

<head>

sowie

<body>

-Tag einfügen und unser Style-Sheet einfügen. Am Ende sieht unsere
"helloworld.html" so aus:

``` html4strict
<html>
<head>
<title>Hallo Welt!</title>

<style type="text/css">
#header {
   background-color: #d5fcf4;
   padding: 0;
   border: 1px solid #0c9;
   margin: 10px;
}

#main {
   background-color: #d5eeff;
   padding: 0;
   border: 1px solid #09f;
   margin: 10px;
   position: relative;
}
</style>
</head>

<body>
<div id="header">
   <core:importdesign namespace="sites\helloworld\pres\templates\menu" template="menu" />
</div>

<div id="main">
   <core:importdesign namespace="sites\helloworld\pres\templates\content" template="content" />
</div>
</body>
</html>
```

Als Ergebnis erhalten wir zwei Bereiche, optisch voneinander getrennt:
Die Menüleiste sowie den Inhaltsbereich. Anhand dieser Struktur können
wir nun an unseren Inhalten und Zielen arbeiten. **Hinweis:** Das
Style-Sheet sollte im späteren Anwendungsfall natürlich getrennt von der
"helloworld.html" behandelt werden. In diesem Beispiel genügt aber die
einfache Definition im

<head>

.

## To Be continued