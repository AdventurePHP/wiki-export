## Einleitung

*Pádraic Brady* hat kürzlich einen
[Blog-Eintrag](http://blog.astrumfutura.com/archives/421-PHP-Framework-Benchmarks-Entertaining-But-Ultimately-Useless.html)
zum Thema Framework-Benchmarks erstellt. In diesem beschreibt er -
angelehnt an einen Blog-Eintrag von *Paul M. Jones* ([How Fast Is Your
Framework?](http://paul-m-jones.com/blog/?p=236)) -, dass Benchmarks
dieser Kategorie sinnlos sind.

Dieser Einwand ist sicher berechtigt, nur validiert man die Performance
eines unveränderten Release-Package, so geben die Zahlen einen guten
Aufschluss darüber, welche Arbeit das Entwickler-Team dem Thema
Performance gewidmet hat. Dies ist vor allem in Zeiten von großen
Performance-Anforderungen (wie beispielsweise Facebook, die den Ansatz
verfolgen, den PHP-Kern schneller zu gestalten) ein nicht unwichtiger
Indikator bei der Auswahl der Entwicklungsbasis.

## Ergebnisse

Der im Blog-Beitrag von *Pádraic Brady* beschrieben Benchmark (verfügbar
unter [1](http://github.com/fabpot/framework-benchs)) wurde mit den im
GIT-Repository verfügbaren Versionen um eine APF-Version (aktueller
SVN-Stand) ergänzt und nochmals ausgeführt. Die Ergebnisse sind wie
folgt:

``` bash
framework                |      rel |      avg |        1 |        2 |        3 |        4 |        5
------------------------ | -------- | -------- | -------- | -------- | -------- | -------- | --------
apf-1.12-beta1-nobench   |   0.3116 |   875.32 |   868.68 |   875.21 |   878.43 |   877.82 |   876.45
apf-1.12-beta1           |   0.2875 |   807.57 |   811.43 |   805.12 |   812.83 |   806.21 |   802.25
baseline-php             |   1.0000 |  2808.70 |  2788.09 |  2833.59 |  2824.45 |  2804.71 |  2792.66
cakephp-1.2.6            |   0.0653 |   183.36 |   177.90 |   183.13 |   185.82 |   184.74 |   185.19
solar-1.0.0beta3         |   0.1806 |   507.33 |   502.21 |   508.75 |   508.77 |   505.52 |   511.39
symfony-1.4.2            |   0.0792 |   222.42 |   219.01 |   222.56 |   223.94 |   223.42 |   223.18
yii-1.1.1                |   0.1713 |   481.26 |   485.52 |   486.86 |   469.46 |   478.76 |   485.72
zend-1.10                |   0.0491 |   137.93 |   137.83 |   137.17 |   136.59 |   136.06 |   141.98
```