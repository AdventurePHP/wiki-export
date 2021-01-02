With release 2.1, the iterator was added support for the extended
templating syntax with issue
[ID\#187](http://tracker.adventure-php-framework.org/view.php?id=187).
Within an *<iterator:item />* tag you can thus access object attributes
via

``` html4strict
${item->getFoo()}
```

during a loop run. In case you are using custom tags within an
*<iterator:item />* you can also access the *item*. In case you want to
format the return value of the *getFoo()* method - if any - you can do
this as follows:

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

Since the instance of the *FooFormatterTag* tag is located within the
*<iterator:item />* DOM tree you can access the *item* via
*getParentObject()*. According to documentation the iterator injects the
current object into data attribute *item*. For this reason,
*getData('item')* on the parent object returns the current item.
<languages />