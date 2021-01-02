Die im Release 1.11 neu entwickelten Formular-Validatoren sehen es vor,
dass auf ein Formular-Feld Listener nach dem Observer- und
Listener-Pattern registriert werden können. Diese werden bei der
Darstellung eines Formulars ausgegeben, sofern das Feld als invalid
markiert wurde. Hierbei spielt es jedoch keine Rolle, welcher Validator
das Event "invalid" ausgelöst hat.

Sofern der Nutzer über die unterschiedlichen Ergebnisse von mehreren
Validierungen informiert werden soll (z.B. "Feld leer", "Feld enthält
keine syntaktisch gültige E-Mail"), so kann das mit der aktuellen
Umsetzung nur durch die Implementierung von eigenen Validatoren gelöst
werden, die das *notify()*-Event selbst interpretieren:

``` php
public function notify(){
   ...
   $controlName = $this->__Control->getAttribute('name');
   $form = $this->__Control->getByReference('ParentObject');
   $listeners = &form->getFormElementsByTagName('form:listener');
   $count = count($listeners);
   for($i = 0; $i < $count; $i++){
      if($listeners[$i]->getAttribute('control') === $controlName && $listeners[$i]->getAttribute('validator') === get_class($this)){
         $listeners[$i]->notify();
      }
   }
}
```

Hierdurch können dedizierte Listener der Form

``` html4strict
<html:form name="email" method="post">
   <form:listener control="email" validator="TextLengthValidator">...</form:listener>
   <form:listener control="email" validator="EMailValidator">...</form:listener>
   <form:text name="foo-text" />
   <form:addvalidator
        class="TextLengthValidator"
        control="email"
        button="button"
   />
   <form:button name="button" />
</html:form>
```

angesprochen werden. Um dies für die kommenden Releases auch
out-of-the-box möglich zu machen, soll das *<form:addvalidator />*-Tag
um ein weiteres Attribut (**type**) erweitert werden. Ist dieses nicht
spezifiziert, wird der Wert **generic** angenommen, andernfalls wird der
Wert **special** als Indikator für obiges Verhalten akzeptiert.

Sofern der Wert also **special** enthält, werden dedizierte Listener
informiert, deren Attribut **name** dem Namen des Formular-Feldes
gleicht und bei dem das Attribut **validator** den Namen des auslösenden
Validators enthält.

Diskussionen zu diesem Proposal gerne unter [Mehrere Listener bei
Formularen](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=258&start=0).