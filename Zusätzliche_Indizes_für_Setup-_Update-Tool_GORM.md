Wie im Forum unter [Hilfe für
APF-Neuling](http://forum.adventure-php-framework.org/de/viewtopic.php?f=1&t=80&p=1915#p1908)
angeregt, sollte es möglich sein, in der Objekt-Definition des GORM
zusätzliche Indizes für definierte Attribute eines Objekts zu
definieren.

Idee der Umsetzung ist es, die Konfigurations-Sektion eines Objekts um
das Attribut **AddKeys** zu erweitern, das eine Pipe-separierte Liste
von Werten enthält, für die zusätzliche Indizes angelegt werden sollen.
Die Definition eines Objekts besitzt dann beispielsweise folgende
Sektion:

``` ini
[User]
FirstName = "VARCHAR(30)"
LastName = "VARCHAR(30)"
...
AddIndices = "FirstName,LastName(UNIQUE)|Foo(FULLTEXT)|Bar(INDEX)"
```

Damit ist es möglich,

-   Indizes über eine oder mehrere Spalten anzulegen und
-   die Art des Index wählbar zu gettalten (INDEX,UNIQUE,FULLTEXT)

Nicht möglich sind **PRIMARY KEY** und spezielle Parametrisierung von
Indizes.

Diskussion der Umsetzung findet im Forum unter [Zusätzliche Indizes für
Setup-/Update-Tool
GORM](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=246)
statt.