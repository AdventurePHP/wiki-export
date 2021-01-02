Mit dem Release 2.1 wurde im Issue
[ID\#187](http://tracker.adventure-php-framework.org/view.php?id=187)
die erweiterte Templating-Sytax auch für den Iterator eingeführt.
Innerhalb des *<iterator:item />*-Tag kann daher beispielsweise mit

``` html4strict
${item->getFoo()}
```

auf die Eigenschaften des Objekts im aktuellen Schleifendurchlauf
zugegriffen werden. Nutzen Sie eigene Tags innerhalb von
*<iterator:item />*, so können Sie darin ebenfalls auf das aktuelle
*item* zugreifen. Möchten Sie beispielsweise die Rückgabe von *getFoo()*
- falls vorhanden - gesondert formatieren, lässt sich das wie folgt
bewerkstelligen:

### Template

``` html4strict
<html:iterator name="...">
   <ul>
      <iterator:item>
         <item:addtaglib
            class="VENDOR\..\FooFormatterTag"
            prefix="item"
            name="foo-formatter"
         />
         <li>
            <strong>${item->getBar()}</strong>
            <item:foo-formatter />
         </li>
      </iterator:item>
   </ul>
</html:iterator>
```

### Tag

``` php
namespace VENDOR\..;

class FooFormatterTag extends Document {

   public function transform() {
      $item = $this->getParentObject()->getData('item');
      $foo = $item->getFoo();
      if (empty($foo)) {
         return '';
      }

      return '<span class="special">' . $foo . '</span>';
   }

}
```

Da die Instanz des *FooFormatterTag*-Tag innerhalb des
*<iterator:item />* im DOM-Baum hängt, kann via *getParentObject()* auf
das *item* zugegriffen werden. Gemäß Dokumentation injiziert der
Iterator das aktuelle Objekt in das Data-Attribut *item*. Aus diesem
Grund können Sie mit *getData('item')* im Eltern-Objekt auf das aktuelle
Item zugreifen. <languages />