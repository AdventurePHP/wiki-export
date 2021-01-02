### Beschreibung

Mit folgendem Code ist es möglich in einem mittels Passwort und dem
Usermanagement Modul gesicherten Bereich dem eingeloggten User zu
erlauben sein Passwort zu ändern.

Controller:

``` php
use APF\modules\genericormapper\data\GenericDomainObject;

class controller extends Base_controller {

    public function transformContent() {
        $form = &$this->getForm('passwordchange');
        $save = &$form->getFormElementByName('change');

        if ($form->isSent() == true && $form->isValid() == true && $save->isSent() == true) {

            //Aktuelle User ID (zb aus einem model holen
            $CMSWAdmin = &$this->getServiceObject('DEV\cmsw\core\CMSWAdminModel', APFService::SERVICE_TYPE_SESSION_SINGLETON);
            $userid = $CMSWAdmin->getUserID();

            //Zugriff auf Usermanagement Modul
            $uM = &$this->getAndInitServiceObject('APF\modules\usermanagement\biz\UmgtManager', 'Default');
            //User Objekt aus der DB holen
            $user = $uM->loadUserByID($userid);

            //Passwort erhalten und setzten
            $passField1 = &$form->getFormElementByName('Password');
            $pass1 = $passField1->getAttribute('value');
            $user->setProperty('Password', $pass1);

            //Speichern
            $uM->saveUser($user);

            //Benachrichtigung erstellen.
            $forwardMessageMgr = &$this->getServiceObject('APF\extensions\forwardmessage\biz\ForwardMessageManager', APFService::SERVICE_TYPE_SESSION_SINGLETON);
            $forwardMessageMgr->addMessage('changepassword', 'Passwort erfolgreich geändert.', true, "message");
        }

        $form->transformOnPlace();
    }
}
```

Template:

``` html4strict
<@controller class="dev\user\changepassword\Controller" @>
<core:addtaglib class="APF\tools\form\taglib\HtmlFormTag" prefix="html" name="form" />

<html:form name="passwordchange" >
    <form:error name="error"><div id="fehler"><ul> </form:error>

                <form:listener control="Password" validator="TextLengthValidator"  >
                    <li>Das Passwort muss mindestens 10 Zeichen haben.</li>
                </form:listener>

                <form:listener control="pass2" validator="FieldCompareValidator">
                    <li>Die beiden Passwörter sind nicht identisch</li>
                </form:listener>

                <form:error name="error"></ul></div> </form:error>

    <label id="password">Passwort: </label>
    <form:password name="Password" ref="pass2"  minlength="10" />

    <label id="password2">Passwort (Nocheinmal): </label>
    <form:password name="pass2"  />

    <form:addvalidator class="APF\tools\form\validator\TextLengthValidator" button="change" control="Password"  mode="strict" type="special"/>
    <form:addvalidator class="APF\tools\form\validator\FieldCompareValidator" control="Password" button="change" type="special"/>
    <form:button name="change" value="Speichern" />
</html:form>
```

### Eingebaute Kontrollmechanismen

Er wird mittels Validator kontrolliert ob die beiden Passwortfelder
gleich ausgefüllt sind und ob die Passwörter eine Mindestlänge von 10
Zeichen haben.

### Verbesserungen/TODOs:

-   Es ist sicher ratsam einen Validator zu schreiben der die Passwörter
    auf Sonderzeichen prüft.
-   Clientvalidatoren einbetten