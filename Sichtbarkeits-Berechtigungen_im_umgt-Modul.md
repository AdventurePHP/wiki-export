## Einleitung

Aktuell (APF-Release 1.11) unterstützt das Modul "usermanagement"
lediglich die Definition von generischen Funktions-Berechtigungen über
Permissions. Diese können dazu genutzt werden, einem Benutzer die
Möglichkeit zu geben eine Aktion innerhalb der Applikation auszuführen
(z.B. Anlegen, Bearbeiten, Löschen eines Objekts) oder eben nicht. Es
fehlt jedoch die Möglichkeit, Sichtbarkeits-Berechtigungen auf Objekte
einer beliebigen Applikation zu vergeben. Hierzu soll im Release 1.12
und 1.13 eine Möglichkeit eingeführt werden, die es zulässt
Anwendungs-Objekte einfach mit Benutzern und Gruppen zu verknüpfen.

## Umsetzungs-Idee

Die Idee der Umsetzung basiert auf einer generischen Struktur aus
Stellvertreter-Objekten der Applikation im umgt-Modul sowie eine
Zuordnung zu Benutzern und Gruppen desselben. Hierdurch können beliebige
Applikationen, Objekte im Modul verwalten lassen. So muss innerhalb der
Applikation nur noch das Handling der Objekte selbst und eine
Schnittstelle/Nutzung des umgt-Moduls implementiert werden.

Das folgende (vorläufige) Domänen-Modell beschreibt die Umsetzung der
Zuweisung der Sichtbarkeits-Rechte:

[Datei:Usermanagement_domain_model_1_13.png](/Datei:Usermanagement_domain_model_1_13.png "wikilink")

## Erweiterung der API des umgtManager

Die API des umgtManager soll so erweitert werden, dass es möglich ist,
Stellvertreter-Objekte und Objekt-Typen zu verwalten (anlegen,
bearbeiten, löschen) und innerhalb der Applikation den Manager dazu
nutzen zu können, die vom Benutzer zugreifbaren Objekte zu selektieren.

### Beispiel: Auslesen

``` php
$uM = $this->getDIServiceObject('APF\modules\usermanagement\biz', 'UmgtManager');

// load objects, the current user is authorized to see
$news = $uM->loadVisibleProxies4User($type, $user);

// load objects, the current user's group is authorized to see
$news = $uM->loadVisibleProxies4Group($type, $group);

// load objects, the current user and it's group is authorized to see
$news = $uM->loadVisibleProxies4UserAndGroup($type, $user, $group);

// remap proxies to the application's real objects
$dataSource = /* ... */
$realNews = array();
foreach($news as $item){
   $realNews[] = $dataSource->loadNewsById($item->getObjectId());
}
```

### Beispiel: Erzeugen

``` php
$uM = $this->getDIServiceObject('APF\modules\usermanagement\biz', 'UmgtManager');

// create new news object within the application
$news = new ApplicationDomainObject4News();
$news->setTitle('foo');
$news->setCreationDate('2010-06-02 17:23:05');
$newsId = $newsManager->saveNews($news);

// authorize the user to see the object
$type = new GenericDomainObject('AppProxyType');
$type->setProperty('AppProxyTypeID', '123');

$proxy = new GenericDomainObject('AppProxy');
$proxy->setProperty('AppObjectId', $newsId);

$uM->createVisibilityDefinition($ype, $proxy, array($user));

// authorize the user's group to see the object
$uM->createVisibilityDefinition($ype, $proxy, array(), array($group));
```