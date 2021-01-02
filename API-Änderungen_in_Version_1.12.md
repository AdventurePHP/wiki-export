In der Version 1.12 sind einige API-Änderungen enthalten, die für
bessere Lesbarkeit des Codes und für ein einfacheres Verständnis der
Klassen sorgen. Mit dem **Revisions-Stand 864** wurden folgende
Änderungen an der API durchgeführt:

-   coreObject -&gt; APFObject
-   baseController -&gt; base_controller
-   connectionManager -&gt; ConnectionManager
-   AbstractFrontControllerAction::set('KeepInURL',...) -&gt;
    AbstractFrontControllerAction::setKeepInUrl(...)
-   AbstractFrontControllerAction::get('KeepInURL') -&gt;
    AbstractFrontControllerAction::getKeepInUrl()
-   AbstractFrontcontrollerAction::set('ActionNamespace',...) -&gt;
    AbstractFrontcontrollerAction::setActionNamespace(...)
-   AbstractFrontcontrollerAction::get('ActionNamespace') -&gt;
    AbstractFrontcontrollerAction::getActionNamespace()
-   AbstractFrontcontrollerAction::set('ActionName',...) -&gt;
    AbstractFrontcontrollerAction::setActionName(...)
-   AbstractFrontcontrollerAction::get('ActionName') -&gt;
    AbstractFrontcontrollerAction::getActionName()
-   AbstractFrontcontrollerAction::set('Type',...) -&gt;
    AbstractFrontcontrollerAction::setType(...)
-   AbstractFrontcontrollerAction::get('Type') -&gt;
    AbstractFrontcontrollerAction::getType()
-   AbstractFrontcontrollerAction::set('Input',...) -&gt;
    AbstractFrontcontrollerAction::setInput(...)
-   AbstractFrontcontrollerAction::get('Input') -&gt;
    AbstractFrontcontrollerAction::getInput()
-   Document::set('Content',...) -&gt; Document::setContent(...)
-   Document::get('Content') -&gt; Document::getContent()
-   APFObject::getByReference('ParentObject') -&gt;
    APFObject::getParentObject()
-   APFObject::setByReference('ParentObject',...) -&gt;
    APFObject::setParentObject(...)
-   APFObject::get('ParentObject') -&gt; APFObject::getParentObject()
-   APFObject::set('ParentObject',...) -&gt;
    APFObject::setParentObject(...)
-   APFObject::set('ObjectID',...) -&gt; APFObject::setObjectId(...)
-   APFObject::get('ObjectID') -&gt; APFObject::getObjectId()
-   APFObject::set('Language',...) -&gt; APFObject::setLanguage(...)
-   APFObject::get('Language') -&gt; APFObject::getLanguage()
-   APFObject::set('Context',...) -&gt; APFObject::setContext(...)
-   APFObject::get('Context') -&gt; APFObject::getContext()
-   Document::get('DocumentController') -&gt;
    Document::getDocumentController()
-   BaseMapper::getByReference('DBDriver') -&gt;
    BaseMapper::getDBDriver()
-   GenericORMapperSetup::set('StorageEngine',...) -&gt;
    GenericORMapperSetup::setStorageEngine(...)
-   GenericORMapperSetup::get('StorageEngine') -&gt;
    GenericORMapperSetup::getStorageEngine()
-   GenericORMapperUpdate::set('StorageEngine',...) -&gt;
    GenericORMapperUpdate::setStorageEngine(...)
-   GenericORMapperUpdate::get('StorageEngine') -&gt;
    GenericORMapperUpdate::getStorageEngine()
-   Page::getByReference('Document') -&gt; Page::getRootDocument()
-   Page::get('Document') -&gt; Page::getRootDocument()
-   APFObject::set('ServiceType',...) -&gt;
    APFObject::setServiceType(...)
-   APFObject::get('ServiceType') -&gt; APFObject::getServiceType()
-   Taglib::set('Prefix',...) -&gt; Taglib::setPrefix(...)
-   Taglib::get('Prefix') -&gt; Taglib::getPrefix()
-   Document-&gt;getByReference('Children') -&gt;
    Document::getChildren()
-   FilterDefinition::set('Namespace',...) -&gt;
    FilterDefinition::setNamespace(...)
-   FilterDefinition::get('Namespace') -&gt;
    FilterDefinition::getNamespace()
-   FilterDefinition::set('Class',...) -&gt;
    FilterDefinition::setClass(...)
-   FilterDefinition::get('Class') -&gt; FilterDefinition::getClass()
-   ExceptionHandlerDefinition::set('Namespace',...) -&gt;
    ExceptionHandlerDefinition::setNamespace(...)
-   ExceptionHandlerDefinition::get('Namespace') -&gt;
    ExceptionHandlerDefinition::getNamespace()
-   ExceptionHandlerDefinition::set('Class',...) -&gt;
    ExceptionHandlerDefinition::setClass(...)
-   ExceptionHandlerDefinition::get('Class') -&gt;
    ExceptionHandlerDefinition::getClass()
-   ErrorHandlerDefinition::set('Namespace',...) -&gt;
    ErrorHandlerDefinition::setNamespace(...)
-   ErrorHandlerDefinition::get('Namespace') -&gt;
    ErrorHandlerDefinition::getNamespace()
-   ErrorHandlerDefinition::set('Class',...) -&gt;
    ErrorHandlerDefinition::setClass(...)
-   ErrorHandlerDefinition::get('Class') -&gt;
    ErrorHandlerDefinition::getClass()...)

Die Änderungen lassen sich durch Search&Replace auf den bestehenden Code
anwenden. Hierzu beispielweise

``` php
->getByReference('ParentObject')
```

durch

``` php
->getParentObject()
```

ersetzen. Diskussion hierzu gerne im Forum unter [1.12
API-Refactoring](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=285)