## Frage

Wie kann man Templates innerhalb einer FC-Action oder anderer Objekte
verwenden und dem Documentcontroller zusätzliche Daten übergeben?

## Antwort

Das einbinden eines Templates funktioniert mit dem Pagecontroller:

``` php
// Seite erzeugen
$page = new Page();

// Seiten-Struktur aufbauen
$page->loadDesign('{1}', '{2}');

// Eventuell Context etc. setzen, in diesem Beispiel aus einem anderen APFObject heraus
$page->setContext($this->getContext());
$page->setLanguage($this->getLanguage());

// Seite transformieren und ausgeben
echo $page->transform();
```

Wobei {1} dem Namespace und {2} dem Name des Templates entsprechen muss.

Für das übergeben von Daten gibt es mehrere Möglichkeiten:

### Methode 1: Füllen eines Singleton-Models

Man erstellt ein beliebiges Model, z.b.:

``` php
class ExampleModel extends APFObject {
    protected $data = null;

    public function setData($data){
        $this->data = $data;
    }

    public function getData(){
        return $this->data;
    }
}
```

Füllt dies vor dem erzeugen von $page und ruft die Daten im
documentcontroller ab. Füllen:

``` php
$model = &$this->getServiceObject('namespace::zum::model', 'ExampleModel');
$model->setData(
    array(
        'foo' => 'bar',
        'bar' => 'foo'
    )
);

$page = new Page();
$page->loadDesign(..., ...);
echo $page->transform();
```

Abrufen:

``` php
$model = &$this->getServiceObject('namespace::zum::model', 'ExampleModel');
$data = $model->getData();
```

Alternativ kann man die Daten auch in der Registry unter einem
bestimmten Wert abspeichern.

### Methode 2: Injizieren der Daten ins Root-Document

Man injiziert die Daten direkt in das Root-Document:

``` php
$page = new Page();
$page->loadDesign(..., ...);
$data = array(
        'foo' => 'bar',
        'bar' => 'foo'
    );
$rootDoc = &$page->getRootDocument();
$rootDoc->setAttribute('dataOrAnything', $data);
```

Und ruft sie im Document-Controller wieder ab:

``` php
$this->getDocument()->getAttribute('dataOrAnything');
```