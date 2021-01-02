Diese Seite beschreibt ein Proposal für Data-Bindings in APF-Formularen.

## Einleitung

Diese Seite beschreibt ein Proposal für Data-Bindings in APF-Formularen.
Ziel ist es, den Code in Document-Controllern für Standardfälle, bei
denen Formularelemente einen direkten Bezug zu Objekten haben zu
minimieren. Die Idee ist angelehnt an die Data-Bindings von ASP.NET,
wenn auch etwas einfacher gehalten.

## Theorie

**Formular-Taglibs**

Die Formular-Tags erhalten 2 zusätzliche Attribute:

-   databinding
-   databinding-option

Mit Hilfe des "databinding" Attributs wird definiert, von welchem Objekt
der anzuzeigende und bearbeitende Wert stammt und wie der Schlüsselname
des Wertes lautet. Über "databinding-option" können zusätzliche Optionen
angegeben werden (Read-Only Werte; Optionale Werte, die im Objekt nur
gesetzt werden, wenn ein Wert angegeben wurde; Werte, deren Inhalt nicht
in das Formularfeld geschrieben werden sollen).

*Beispiel:*

``` xml
<form:text name="FirstName" maxlength="100" databinding="user.firstName" />
```

Das Feld "FirstName" wird an den Wert des Feldes "firstName", des
mittels "user" adressierten Objektes, gebunden. (Mehr dazu weiter unten)

**Documentcontroller**

Im Controller soll es möglich sein, dem Formular Databinding-Objekte zu
übergeben. Diese werden über einen Namen adressiert.

*Beispiel:*

``` php
$user = $uM->loadUserByID($userId);
$form->addDataBinding('user', $user);
```

## Vollständiges Beispiel:

Im Folgenden habe ich mir ein Formular des Usermanagements genommen, und
nach obigem Muster überarbeitet. Es handelt sich um das Template
"user_form.html" und den Controller "UserEditController". Um die
Besonderheiten des Passwort-Feldes abzudecken, habe ich 2 Optionen
eingeführt:

-   OPTIONAL : dient dazu, zu definieren, dass dieses Feld NUR gesetzt
    werden soll, wenn dieser Wert nicht leer ist.
-   NOPRESET : dient dazu, zu definieren, dass in dieses Feld kein Wert
    gesetzt werden soll, sondern dieses nur ausgelesen werden soll.

**Vergleich alt vs. neu:**

Die Datei "user_form.html" hat die selbe Anzahl an Zeilen. lediglich
1-2 Attribute kamen pro Formularfeld dazu. Der Unterschied ist aber im
Controller beträchtlich: In der aktuellen SVN-Version von 2.0 hat die
Klasse **110 Zeilen** Code. Die unten gezeigte Version hat nur noch **42
Zeilen**. Das verringert die Fehleranfälligkeit und hilft bei der
Übersicht.

**user_form.html**

``` xml
<core:addtaglib class="APF\tools\form\taglib\HtmlFormTag" prefix="html" name="form" />
<html:form name="UserForm" method="post">
   <div>
      <form:addvalidator
         class="APF\tools\form\validator\TextLengthValidator"
         control="FirstName|LastName|Username"
         button="UserButton"
      />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.first-name.label"/></label>
      <form:text name="FirstName" maxlength="100" databinding="user.firstName" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.last-name.label"/></label>
      <form:text name="LastName" maxlength="100" databinding="user.lastName" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.street-name.label"/></label>
      <form:text name="StreetName" maxlength="100" databinding="user.streetName" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.street-number.label"/></label>
      <form:text name="StreetNumber" maxlength="100" databinding="user.streetNumber" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.zip.label"/></label>
      <form:text name="ZIPCode" maxlength="100" databinding="user.zIPCode" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.city.label"/></label>
      <form:text name="City" maxlength="100" databinding="user.city" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.email.label"/></label>
      <form:text name="EMail" maxlength="100" databinding="user.eMail" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.mobile.label"/></label>
      <form:text name="Mobile" maxlength="100" databinding="user.mobile" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.user-name.label"/></label>
      <form:text name="Username" maxlength="100" databinding="user.userName" />

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.password.label"/></label>
      <form:password name="Password" optional="true" ref="Password2" maxlength="100" databinding="user.password" databinding-option="OPTIONAL|NOPRESET"/>

      <label><form:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.repeat.label"/></label>
      <form:password name="Password2" optional="true" maxlength="100" />
      <form:addvalidator
            class="APF\tools\form\validator\FieldCompareValidator"
            button="UserButton"
            control="Password"
            />
      <form:addvalidator
            class="APF\tools\form\validator\TextLengthValidator"
            button="UserButton"
            control="Password|Password2"
            />
      <p>
         <form:button name="UserButton">
            <button:getstring
               namespace="APF\modules\usermanagement\pres"
               config="labels.ini"
               entry="frontend.user.edit.form.button.label"
               />
         </form:button>
         <form:hidden name="userid" databinding="user.userId" />
      </p>
   </div>
</html:form>
```

**UserEditController.php**

``` php
class UserEditController extends UmgtBaseController {

   /**
    * @public
    *
    * Displays and handles the user edit form.
    *
    * @author Christian Achatz
    * @version
    * Version 0.1, 26.12.2008<br />
    * Version 0.2, 02.01.2009 (Added the password fields handling)<br />
    */
   public function transformContent() {

      // get the userid from the request
      $userId = RequestHandler::getValue('userid');

      // setup the form
      $form = & $this->getForm('UserForm');

      // get the manager
      $uM = & $this->getManager();

      if ($form->isSent() == true) {
        $user = new UmgtUser();
        $user->setObjectId($userId);
        $form->addDataBinding('user', $user);

         if ($form->isValid() == true) {
            // read bound user with values from form fields
            $user = $form->getDataBinding('user');
            $uM->saveUser($user);
            HeaderManager::forward($this->generateLink(array('mainview' => 'user', 'userview' => '', 'userid' => '')));
            return;
         }
      } else {
         $user = $uM->loadUserByID($userId);
         $form->addDataBinding('user', $user);
      }
      $form->transformOnPlace();
   }
}
```