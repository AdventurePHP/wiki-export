Das GenericCriterionObject unterstützt ab Version 1.13 die (optionale)
Verwendung als "Fluent Interface". Alle add\*Indicator() Methoden geben
hierfür die aktuelle Instanz des GenericCriterionObject zurück.

**Die bisherige Verwendung sah beispielsweise so aus:**

``` php
$criterion = new GenericCriterionObject();
$criterion->addCountIndicator(1);
$criterion->addRelationIndicator('xxx', $sourceObject);
$criterion->addOrderIndicator('name');
$criterion->addPropertyIndicator($attributeName, $attributeValue);
```

**Mithilfe des Fluent Interface kann selbiges nun so geschrieben
werden:**

``` php
$criterion = new GenericCriterionObject();
$criterion
    ->addCountIndicator(1)
    ->addRelationIndicator('xxx', $sourceObject)
    ->addOrderIndicator('name')
    ->addPropertyIndicator($attributeName, $attributeValue);
```