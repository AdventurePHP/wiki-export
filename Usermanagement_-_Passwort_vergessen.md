Das Usermanagement-Modul wurde mit der APF-Version 3.2 um eine
Passwort-vergessen Funktion versehen. Anhand dessen kann ein
registrierter Benutzer ein Token zur Neuerstellung eines Passwortes
erstellen. Dieses Token wird per E-Mail versendet und kann in der länge
der Gültigkeit angepasst werden.

## Einleitung

Das Usermanagement-Modul (folgend *UMGT*) bietet bereits alle fertigen
Komponenten: Template, Controller sowie Konfiguration (mitsamt der
Texte). Nachfolgend werden die Komponenten kurz erläutert. Die
Vorgehensweise zum Passwort-Reset besteht immer aus zwei Schritten:

1.  Passwort vergessen (forgot_pw) -&gt; Token erstellen
2.  Passwort resetten (reset_pw) -&gt; Token einlösen und neues
    Passwort erstellen

## Komponenten

### Templates

Die fertigen Templates findest du unter:

``` dos
modules/
    usermanagement/
        pres/
            templates/
                login/
                    forgot_pw.html
                    reset_pw.html
```

Diese Templates nutzen die APF-\*:getstring-Tags, worüber alle Texte aus
der sprachabhängigen Konfiguration geladen werden. Weiterhin wird zum
Spamschutz *ReCaptcha* verwendet. Im Template ist es daher erforderlich,
deinen private- und public-key einzutragen. [Die ReCaptcha-Dokumentation
dafür findest du
hier.](https://adventure-php-framework.org/Seite/150-ReCaptcha)

### Controller

Die Controller findest du unter:

``` dos
modules/
    usermanagement/
        pres/
            documentcontroller/
                login/
                    ForgotPasswordController.php
                    ResetPasswordCpontroller.php
```

Zur Token-Erstellung wird die Konfiguration berücksichtigt. Anhand der
Konfiguration kannst du die Token-Gültigkeit sowie die URL zum Resetten
des Passwortes festlegen. Standardmäßig werden folgende Werte verwendet:

-   Gültigkeit: 24 Stunden
-   Reset-URL:
    http(s)://(www.)example.tld/(?some=page)&user=reset_pw&h=%TOKEN%

Wie zu sehen ist, werden der aktuellen URL zwei neue Parameter
angehängt: *user* und *h*. Beachte bitte, dass die URL über den
*LinkGenarator* erstellt wird. Solltest du die URL-Rewrite-Engine
nutzen, wird dies automatisch berücksichtigt.

### Konfiguration

#### Verhalten verändern

Die Konfiguration zum Ändern der eben angesprochenen Parameter findest
du unter:

``` dos
config/
    modules/
        usermanagement/
            pres/
                EXAMPLE_forgotpw.ini
```

und besteht aus folgenden Parametern:

-   hash.lifetime - Definiert die Gültigkeit des Tokens
-   reset.url - Dient zum Ändern der URL

Der Parameter, der bei *reset.url* eingetragen ist, wird dabei immer an
den Host angehangen. Ebenso wird ein vorhandenes Schema und ein
angegebener Port mit verwendet. Wenn du also folgende URL zum Resetten
des Passwortes nutzen möchtest:
*<http://www.beispiel.tld/konto/benutzer/passwort/reset>* gilt es
folgendes als *reset.url* einzutragen: *konto/benutzer/passwort/reset*.
Der Parameter *h* mit dem entsprechenden Token wird automatisch
angehangen.

Ein weiteres Beispiel: URL: <https://example.tld/?p=user&reset=pw>
reset.url: p=user&reset=pw

#### Texte ändern

Um die Texte im Formular zu ändern kannst du die \*_labels.ini
anpassen. Dort findest du auch die Texte, welche beim E-Mail-Versand
genutzt werden.

### E-Mail-Versand

Zum Versenden der E-Mail wird die APF-eigene *Message*-Klasse verwendet.
Aus Gründen der Kompatibilität mit dem vorhergehenden *mailsender* wird
eine Konfiguration benötigt:

``` dos
config/
    Tools/
        mail/
            EXAMPLE_mailsender.ini
```

Diese Konfiguration benötigt die Sektion *UmgtForgotPassword*. Ein
Beispiel:

``` ini
[UmgtForgotPassword]
Mail.SenderName = "Dein Projekt.de - Passwort vergessen"
Mail.SenderEMail = "konto@dein-projekt.de"
Mail.ReturnPath = "noreply@dein-projekt.de"
Mail.ContentType = "text/html; charset=UTF-8"
```

Via ContentType = "text/html; charset=UTF-8" kannst du E-Mails mit
HTML-Inhalt versenden. Du kannst dadurch im Text der E-Mail
beispielsweise

``` html
<br/>
```

-Tags für Zeilenumbrüche nutzen.

Bei weiteren Fragen steht dir gerne das
[Forum](https://adventure-php-framework.org/forum/index.php) zur
Verfügung.