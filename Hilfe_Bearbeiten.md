# Kapitel

Kapitel lassen sich mit = realisieren:

=1. Kapitel= ==1.1. Kapitel== ===1.1.1. Kapitel===

# Aufzählungen

Aufzählungen werden mit einem \* erreicht:

-   1\. Punkt
-   2\. Punkt
-   3\. Punkt
-   4\. Punkt

# Code-Formatierung

## PHP Code

Erreichbar durch &lt;source lang="php"&gt;&lt;/source&gt;

``` php
<?php $foo = $bar; ?>
```

## HTML Code

Erreichbar durch &lt;source lang="html4strict"&gt;&lt;/source&gt;

``` html4strict
<head>
  <title>Schöne Seite</title>
</head>
```

## CSS Code

Erreichbar durch &lt;source lang="css"&gt;&lt;/source&gt;

``` css
p {
  font: italic small-caps bold 1em/1.5em verdana, sans-serif;
}
```

## Ini Code - Config

Erreichbar durch &lt;source lang="ini"&gt;&lt;/source&gt;

``` ini
[Config]
item = "value"
```

## SQL Code

Erreichbar durch &lt;source lang="sql"&gt;&lt;/source&gt;

``` sql
CREATE TABLE IF NOT EXISTS `test` (
  `testID` INT(5) UNSIGNED NOT NULL auto_increment,
  `CreationTimestamp` timestamp NOT NULL default CURRENT_TIMESTAMP,
  `ModificationTimestamp` timestamp NOT NULL default '0000-00-00 00:00:00',
  PRIMARY KEY (`testID`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
```