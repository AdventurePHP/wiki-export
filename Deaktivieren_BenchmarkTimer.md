Mit dem Release 1.11 ist es nun möglich, den
[BenchmarkTimer](http://adventure-php-framework.org/Seite/004-Benchmark)
im Live-Betrieb zu deaktivieren. Hierzu muss in der Bootstrap Datei wie
gewohnt eine Singleton-Instanz erzeugt und als deaktiviert markiert
werden:

``` php
$bench = &Singleton::getInstance('BenchmarkTimer');
$bench->disable();
```

Empirische Messungen belegen, dass sich dadurch die Performance eines
durchschnittlich komplexen Requests (vergleiche
[adventure-php-framework.org](http://adventure-php-framework.org)) um
bis zu 25% steigern lässt. Da der Benchmarker in Produktions-Umgebungen
keine notwendige Funktion erfüllt, ist das Abschalten des Tools mit
keinen Nachteilen verbunden.