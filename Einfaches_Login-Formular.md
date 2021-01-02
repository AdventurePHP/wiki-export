Dieses HOWTO beschreibt ein einfaches Login-Formular, das zur
Authentifizierung von Benutzern genutzt werden kann. Als Ergebnis eines
erfolgreichen Logins soll der Benutzer anschließend auf diejenige Seite
geleitet werden, die er zuvor besucht hat. Wurde kein Referer gefunden,
wird ihm statt des Formulars eine Erfolgsmeldung angezeigt.

## Einstieg

Um eine schnelle Entwicklungsbasis zu haben, nutzen wir die aktuelle
Sandbox des APF. Diese ist bereits eine vollständig funktionierende
Webseite, in die wir die Login-Funktionalität einbinden können. Das
HOWTO geht davon aus, dass ein lokal installierter Webserver vorhanden
ist.

Laden wir uns zunächst das aktuelle **apf-demopack-\*.zip** unter
[1](http://adventure-php-framework.org/Seite/008-Downloads) herunter und
entpacken es in einen Ordner unterhalb des
Webserver-Root-Verzeichnisses. In diesem Fall nutzen wir den Ordner
**apf-sandbox**. Rufen wir nun die URL *<http://localhost/apf-sandbox>*
auf, wird die Sandbox-Startseite angezeigt.

## Aufbau der Sandbox

Um unsere Login-Funktionalität zu integrieren ist es zunächst
interessant, wie die Sandbox technisch aufgebaut ist und an welcher
Stelle wir uns *einhängen* können.

Die Templates der Seite sind unterhalb
*/apf-sandbox/apps/sandbox/pres/templates/* abgelegt, die zugehörigen
Controller unterhalb von */apf-sandbox/apps/sandbox/pres/controller/*.
Die Datei */apf-sandbox/apps/sandbox/pres/templates/main.html* ist das
Haupt-Template, das die jeweils relevanten Inhalte für die Bereiche
Navigation, Inhalts-Bereich und Footer sprachabhängig aus den Ordnern
*/apf-sandbox/apps/sandbox/pres/templates/de/* bzw.
*/apf-sandbox/apps/sandbox/pres/templates/en/* einbindet.

Da wir die deutsche Version der Webseite nutzen, ist der Ordner
*/apf-sandbox/apps/sandbox/pres/templates/de/* bzw. das Inhalts-Template
*/apf-sandbox/apps/sandbox/pres/templates/de/content.html*für uns
interessant. Dieses bindet über den
[<core:importdesign />](http://adventure-php-framework.org/Seite/046-Standard-TagLibs#Chapter-1-2-Importdesign)-Tag
die gewünschten Inhalts-Templates aus dem Ordner
*/apf-sandbox/apps/sandbox/pres/templates/de/content/* ein. Hierzu wird
im Template *content.html* die Eigenschaft des Tags genutzt, Templates
basierend auf URL-Parameter auszuwählen. Exakt diesen Mechanismus wollen
wir uns zu Nutze machen um das Login in die Seite zu integrieren. Eine
genauere Beschreibung des Aufbaus der Sandbox findet sich auf der Seite
**Template-Wizzard**. Der Wizzard auf der genannten Seite kann dazu
verwendet werden, neue Seiten-Templates anzulegen.

## Komponenten des Login-Formulars

Das APF verfolgt das Konzept der Integration von Modulen und Komponenten
einer Software auf der GUI-Ebene. Grund ist, dass Module und Komponenten
auf GUI-Ebene deutlich mehr Gemeinsamkeiten als auf anderen Schichten
haben. Aus diesem Grund beschäftigen wir uns zunächst mit Template, das
später die Ausgabe des Login-Formulars erzeugen soll.

Legen wir zunächst ein Template mit dem Namen login.html in Ordner
*/apf-sandbox/apps/sandbox/pres/templates/de/content* an. Dieses wird
zunächst mit dem Inhalt

``` html4strict
Login-Formular
```

gefüllt. Rufen wir nun die URL
*<http://localhost/apf-sandbox/?page=login>* auf, wird der in der
Code-Box beschriebene Inhalt im Inhalts-Bereich der Seite angezeigt.

Nun können wir dazu übergehen, das Formular zu definieren und den
Controller zu implementieren.

## Aufbau des Formulars

Unter
[Formulare](http://adventure-php-framework.org/Seite/113-Formulare)
finden sich alle vorhandenen Formular-Elemente, auf der Seite
[Verwendung von
Formularen](http://adventure-php-framework.org/Seite/114-Verwendung-von-Formularen)
sind einige Beispiele rund um Formulare beschrieben.

Zunächst widmen wir uns aber dem Aufbau des Formulars. In unserem Fall
sind das je ein Eingabe-Feld für Benutzer-Name und Passwort sowie ein
Buttom zum Absenden. Die Definition des Formulars gestaltet sich also
wie folgt:

``` xml
<core:addtaglib class="APF\tools\form\taglib\HtmlFormTag" prefix="html" name="form" />
<html:form class="login-box" name="Login">
   <fieldset>
      <label for="login-username">Benutzer:</label>
      <form:text id="login-username" name="username" />

      <label for="login-password">Passwort</label>
      <form:password id="login-password" name="password" />

      <form:button name="login" value="Login" />
   </fieldset>
</html:form>
```

Die Formatierung des Formulars ist nicht Teil dieses HOWTOs, eine
angenehme Anzeige der Felder kann jedoch mit den folgenden CSS-Styles
erreicht werden:

``` css
.login-box fieldset {
   border: none;
   margin: 0;
   padding: 0;
}
.login-box label,
.login-box input {
   display: block;
}
.login-box input {
   border: 1px solid black;
}
.login-box input[type='submit'] {
   margin: 1em 0;
}
.login-box input[type='text'] ,
.login-box input[type='password'] {
   width: 14em;
}
```

## Anzeige des Formulars

Da ein Formular immer eine Reaktion des Systems auf Benutzer-Eingaben
erfordert, werden APF-Formulare nicht direkt sondern nur mit Hilfe eines
Controllers angezeigt.

Aus diesem Grund legen wir nun wie unter
[Controller](http://adventure-php-framework.org/Seite/006-Controller)
beschrieben die Datei
*/apf-sandbox/apps/sandbox/pres/controller/LoginController.php* an.
Diese erhält zunächst folgenden Inhalt:

``` php
class LoginController extends BaseDocumentController {

   public function transformContent() {
      $this->getForm('Login')->transformOnPlace();
   }

}
```

Mit diesem Quellcode wird die aktuelle Instanz des Formulars **Login**
bezogen und per **transformOnPlace()** dort ausgegeben, wo das Formular
im Template definiert wurde.

Um das Formular nun wirklich anzuzeigen, ist es erforderlich, dem
Template denjenigen Controller zuzuweisen, der für die Ausgabe zuständig
ist. Das lässt sich mit einem

``` xml
<@controller
   class="APF\sandbox\pres\controller\LoginController"
@>
```

zu Beginn des Templates erledigen. Rufen wir nun die Seite
*<http://localhost/apf-sandbox/?page=login>* erneut auf, wird uns das
Login-Formular angezeigt.

## Validierung der Eingaben

Das Formular beinhaltet bisher keine Validierung der Eingaben. Deutlich
zu erkennen ist dieses Verhalten, wenn wir auf der Login-Seite den
Button klicken.

Um dem Benutzer eine Rückmeldung über fehlende oder falsche Eingaben zu
geben, fügen wir nun zunächst Feld-Validatoren hinzu, die leere oder zu
kurze Eingaben monieren. Das geht so:

``` xml
<html:form class="login-box" name="Login">
   <fieldset>
      <label for="login-username">Benutzer:</label>
      <form:text id="login-username" name="username" />

      <label for="login-password">Passwort</label>
      <form:password id="login-password" name="password" />

      <form:addvalidator
          class="TextLengthValidator"
          button="login"
          control="username|password"
      />

      <form:button name="login" value="Login" />
   </fieldset>
</html:form>
```

In der Standard-Konfiguration des
[Validators](http://adventure-php-framework.org/Seite/113-Formulare#Chapter-4-1-1-TextLengthValidator)
werden leere Eingaben und Eingaben &lt; 3 Zeichen als Fehler markiert.

Rufen wir die Login-Seite erneut auf und senden das Formular ab, werden
die beiden Felder rot umrandet. Dies wird dadurch erreicht, dass der
Validator dem Feld die CSS-Klasse *apf-form-error* zuweist, die im CSS
der Sandbox mit einem roten Rand belegt ist.

## Authentifizierung

Die Authentifizierung kann nun im Controller implementiert werden. Ein
weiterer Bestandteil ist die Weiterleitung des Benutzers auf die zuletzt
besuchte Seite. Um das Verhalten einfacher testen zu können, fügen wir
die Login-Seite zum Menü hinzu. So können wir ausgehend von einer
beliebigen Seite die Login-Seite aufrufen und erwarten als Positiv-Fall
die Weiterleitung auf die zuletzt gesehene Seite.

Zu diesem Zweck adaptieren wie die Datei
*/apf-sandbox/apps/sandbox/pres/templates/de/navi.html* wie folgt:

``` html4strict
<ul>
   <li><!-- --></li>
   <li><a href=".">Startseite</a></li>
   <li><a href="?page=docs">Dokumentation</a></li>
   <li><a href="?page=db-wizzard">Datenbank-Wizzard</a></li>
   <li><a href="?page=tmpl-wizzard">Template-Wizzard</a></li>
   <li><a href="?page=examples">Taglib-Beispiel</a></li>
   <li><a href="?page=login">Login</a></li>
</ul>
```

Nun können wir uns der Logik des Controllers widmen. Es gilt folgende
Fälle zu berücksichtigen:

1.  Login-Seite wird aufgerufen
2.  Formular wird mit demantisch richtigen Daten abgeschickt
    1.  Prüfung der Daten schlägt fehl
    2.  Prüfung der Daten kann erfolgreich abgeschlossen werden

### Anzeige der Login-Seite

Wird die Login-Seite angezeigt, müssen wir zunächst analysieren, von
welcher Seite der Benutzer gekommen ist und uns die Seite merken. Dies
lässt sich wie folgt realisieren:

``` php
$form = $this->getForm('Login');

if(!$form->isSent()){
   $referer = isset($_SERVER['HTTP_REFERER']) ? $_SERVER['HTTP_REFERER'] : null;
   $session = new SessionManager('APF\sandbox\login');
   $session->saveSessionData('LoginStartPage', $referer);
}
```

### Prüfung der Benutzer-Eingaben

Hat der Benutzer das Formular mit für den Validator validen Daten
abgesendet muss eine Prüfung der Benutzer-Daten erfolgen. Sind diese
valide, wird der Benutzer zur Ausgangsseite weitergeleitet, enthalten
Sie Fehler wird ein entsprechender Hinweis angezeigt.

Zur Prüfung der Benutzer-Eingaben implementieren wir eine Methode
*checkCredentials($username, $password)* innerhalb des Controllers.
Diese liefert im aktuellen HOWTO immer true zurück sobald die
Kombination admin/admin eingegeben wird, muss jedoch für den konkreten
Anwendungsfall angepasst werden. Die Prüfung der Eingaben kann wie folgt
im Controller realisiert werden:

``` php
if ($form->isSent() && $form->isValid()) {

   $usernameControl = $form->getFormElementByName('username');
   $passwordControl = $form->getFormElementByName('password');
   $username = $usernameControl->getAttribute('value');
   $password = $passwordControl->getAttribute('value');

   if ($this->checkCredentials($username, $password)) {
      $loginStartPage = $session->loadSessionData('LoginStartPage');
      if ($loginStartPage == null) {
         $this->getTemplate('LoginSuccess')->transformOnPlace();
      } else {
         HeaderManager::forward($loginStartPage);
      }
   } else {
      // Markierung der fehlerhaften Felder ...
   }
} else {
   $form->transformOnPlace();
}
```

In der ersten Bedingung wird abgeprüft, ob das Formular valide
abgesendet wurde. Falls nicht, wird das Formular entsprechend wieder
angezeigt. Falls es korrekt ausgefüllt wurde, werden die
Benutzer-Eingaben aus den beiden Formular-Elementen bezogen. Diese
Vorgehensweise hat den Vorteil, dass evtl. konfigurierte
[Filter](http://adventure-php-framework.org/Seite/113-Formulare#Chapter-5-Filter)
bereits auf die Benutzer-Eingaben angewendet sind.

Anschließend wird über die Methode *checkCredentials()* geprüft, ob die
Eingaben korrekt sind. Ist das der Fall wird die gemerkte Referer-Seite
aus der Session bezogen und eine Weiterleitung initiiert. Ist kein
Redirect möglich, da die Seite nicht bekannt ist, soll der Inhalt des
Templates **LoginSuccess** statt dem Redirect angezeigt werden. Um dies
zu ermöglichen, muss dieses innerhalb des Templates *login.html* noch
definiert werden. Z.B. so:

``` xml
<html:template name="LoginSuccess">
   <p>Sie sind erfolgreich eingeloggt worden.</p>
</html:template>
```

### Ausgabe der Fehlermeldung

Konnten die semnatisch korrekten Eingaben nicht erfolgreich validiert
werden soll eine Fehlermeldung angezeigt werden. Dies kann auf mehrere
Arten realisiert werden:
[<form:error />](http://adventure-php-framework.org/Seite/113-Formulare#Chapter-3-19-Error)-Tag
oder einem externen Template, das im Fehlerfall in einen
[<form:placeholder />](http://adventure-php-framework.org/Seite/113-Formulare#Chapter-3-13-Platzhalter)-Tag
injiziert wird.

Für diesen Anwendungsfall entscheiden wir uns für die erste Option. Um
einem potentiellen Angreifer nicht die Möglichkeit zu bieten
herauszufinden, welche der Eingaben falsch war, markieren wir beide
Eingabe-Felder als fehlerhaft und formulieren einen neutralen Text. Die
Markierung der Felder kann im else-Zweig wie folgt vorgenommen werden:

``` php
$usernameControl->markAsInvalid();
$passwordControl->markAsInvalid();
```

Die Ausgabe des Fehlers wird im Formular wie folgt realisiert:

``` xml
<form:error>
   <p class="error">
      Die eingegebenen Benutzer-Daten konnten sind fehlerhaft. Bitte prüfen Sie ihre Eingaben.
   </p>
</form:error>
```

## Vollständiger Quellcode

Um das Formular bei fehlerhaften Eingaben nochmals anzuzeigen muss der
komplette Code des Controllers wie folgt aussehen:

``` php
namespace APF\sandbox\pres\controller;

use APF\core\session\SessionManager;
use APF\tools\http\HeaderManager;

class LoginController extends BaseDocumentController {

   public function transformContent() {

      $form = $this->getForm('Login');

      $session = new Session('APF\sandbox\login');

      if (!$form->isSent()) {
         $referer = isset($_SERVER['HTTP_REFERER']) ? $_SERVER['HTTP_REFERER'] : null;
         $session->save('LoginStartPage', $referer);
      }

      if ($form->isSent() && $form->isValid()) {

         $usernameControl = $form->getFormElementByName('username');
         $passwordControl = $form->getFormElementByName('password');
         $username = $usernameControl->getValue();
         $password = $passwordControl->getValue();

         if ($this->checkCredentials($username, $password)) {
            $loginStartPage = $session->load('LoginStartPage');
            if ($loginStartPage == null) {
               $this->getTemplate('LoginSuccess')->transformOnPlace();
            } else {
               HeaderManager::forward($loginStartPage);
            }
         } else {
            $usernameControl->markAsInvalid();
            $passwordControl->markAsInvalid();
            $form->transformOnPlace();
         }
      } else {
         $form->transformOnPlace();
      }
   }

   private function checkCredentials($username, $password) {
      return $username == 'admin' && $password == 'admin';
   }

}
```

Der Inhalt des Templates gestaltet sich wie folgt:

``` xml
<@controller
   class="APF\sandbox\pres\controller\LoginController"
@><style type="text/css">
   .login-box fieldset {
      border: none;
      margin: 0;
      padding: 0;
   }
   .login-box label,
   .login-box input {
      display: block;
   }
   .login-box input {
      border: 1px solid black;
   }
   .login-box input[type='submit'] {
      margin: 1em 0;
   }
   .login-box input[type='text'] ,
   .login-box input[type='password'] {
      width: 14em;
   }
   .login-box .error {
      background: red;
      color: white;
      padding: 0.5em;
   }
</style>
<core:addtaglib class="APF\tools\form\taglib\HtmlFormTag" prefix="html" name="form" />
<html:form class="login-box" name="Login">
   <fieldset>
      <form:error>
         <p class="error">
            Die eingegebenen Benutzer-Daten konnten sind fehlerhaft. Bitte prüfen Sie ihre Eingaben.
         </p>
      </form:error>

      <label for="login-username">Benutzer:</label>
      <form:text id="login-username" name="username" />

      <label for="login-password">Passwort</label>
      <form:password id="login-password" name="password" />

      <form:addvalidator
         class="APF\tools\form\validator\TextLengthValidator"
         button="login"
         control="username|password"
         />

      <form:button name="login" value="Login" />
   </fieldset>
</html:form>

<html:template name="LoginSuccess">
   <p>Sie sind erfolgreich eingeloggt worden.</p>
</html:template>
```