Das HTML-Markup des Kontakt-Formular-Moduls wurde im Zuge des Release
1.11 und dem damit verbundenen Relaunch der APF-Seite aktualisiert. Die
Änderungen sind:

-   Inline-Styles wurden entfernt.
-   Inkludierte ''
    <style />

    ''-Blöcke wurden in eigenes CSS-File ausgelagert.

Um das Auftreten des Moduls nun zu bestimmen, kann die mitgelieferte
CSS-Datei (*/modules/contact/pres/css/contact.css*) oder eine eigene
genutzt werden. Dieses muss in den ''

<head />

''-Block der jeweiligen Webseite eingebunden werden.

Die folgenden Code-Boxen zeigen das neue HTML-Grundgerüst:

#### Formular

``` html4strict
<div class="contact-main">
    <h2>...</h2>

    <p>...</p>

    <div class="contact-form">
        <form name="contact" method="post">
            <div>
                <div class="error-container">
                    <ul>
                        <li>...</li>
                    </ul>
                </div>
                <label for="contact-form-recipient">...</label>
                <select id="contact-form-recipient" name="Empfaenger"/>
                <label for="contact-form-sendername">...</label>
                <input type="text" id="contact-form-sendername" name="AbsenderName"/>
                <label for="contact-form-recipient-email">...</label>
                <input type="text" id="contact-form-recipient-email" name="AbsenderAdresse"/>
                <label for="contact-form-subject">...</label>
                <input type="text" id="contact-form-subject" name="Betreff"/>
                <label for="contact-form-textarea">...</label>
                <textarea id="contact-form-textarea" name="Text" cols="50" rows="6"></textarea>

                <div class="fullsizebox captchabox">
                    <label for="contact-form-captcha">...</label>
                    <img/>
                    <input type="text" id="contact-form-captcha"/>
                </div>
                <div class="fullsizebox buttonbox">
                    <input type="submit" name="send"/>
                </div>
            </div>
        </form>
    </div>
</div>
```

#### Meldung

``` html4strict
<div class="contact-main">
   <h2>...</h2>
   <p>...</p>
</div>
```