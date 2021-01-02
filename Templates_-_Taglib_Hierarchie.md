<div style="border: 2px solid #fb3; background: #ff8; color: #333; margin: 1em 0; min-height: 3em; padding: 10px 10px 10px 10px;">

Bitte beachten Sie, dass diese Dokumentation für die Versionen 1.X und
2.X **jedoch nicht für 3.X** gilt. Ab Version 3.0 werden Tags an Hand
ihrer Definition im Template in jeder beliebigen Hierarchie erkannt.
Ferner werden Tags global registriert und stehen damit in jeder
Hierarchie zur Verwendung zur Verfügung.

</div>

Folgende Erläuterung zur Funktionsweise des APF-Parsers für Taglibs kann
bei möglichen Problemen, vor allem beim Erstellen eigener Taglibs,
hilfreich sein.

Der Parser erkennt aus Performancegründen keine direkten Hierarchien im
Quellcode. Alle Taglibs die dem Parser bekannt sind (z.B. im Konstruktor
einer Taglib oder per \*:addtaglib bekannt gemacht) werden der Reihe
nach wie sie hinzugefügt wurden geparst.

**Sehen wir uns ein kleines Beispiel mit Pseudocode an:**

``` xml
<html:template name="example">
    <template:addtaglib namespace=".." prefix="template" class="anytaglib" />
    <template:addtaglib namespace=".." prefix="template" class="exampletaglib" />

    <template:placeholder name="anything" />
    <template:anytaglib>
        <template:placeholder name="anotherone" />
        <template:exampletaglib />
    </template:anytaglib>
</html>
```

Hier würde man möglicherweise erwarten, dass der Placeholder
"anotherone" und "exampletaglib" als Kind von "anytaglib" geparst wird.
Doch genau dies ist nicht der Fall, denn:

**1. Placeholder:** Der Placeholder wurde bereits im Konstruktor der
template-taglib bekannt gemacht, während "anytaglib" erst beim Parsen
bekannt gemacht wurde. Deshalb werden alle Vorkommen von
&lt;template:placeholder \[..\] /&gt; innerhalb des Templates gesucht
und als Kind des Templates eingehängt. Erst danach wird "anytaglib"
geparst.

**2. exampletaglib** Hier ist der Fall genau umgekehrt: "anytaglib"
wurde vor "exampletaglib" bekannt gemacht, weshalb zuerst "anytaglib"
geparst wird. Der Inhalt davon wird nun nicht mehr geparst, weshalb
"exampletaglib" niemals geparst wird. Man müsste hier das :addtaglib aus
dem Template in "anytaglib" verschieben. Da man dann allerdings auf das
selbe Problem wie mit dem Placeholder stoßen würde, sähe die korrekte
Lösung obigen Templates so aus:

``` xml
<html:template name="example">
    <template:addtaglib namespace=".." prefix="template" class="anytaglib" />

    <template:placeholder name="anything" />
    <template:anytaglib>
        <anytaglib:addtaglib namespace=".." prefix="anytaglib" class="exampletaglib" />
        <anytaglib:placeholder name="anotherone" />
        <anytaglib:exampletaglib />
    </template:anytaglib>
</html>
```

Wobei davon ausgegangen wird, dass wenigstens "anytaglib:addtaglib" und
"anytaglib:placeholder" im Konstruktor von "template:anytaglib" bekannt
gemacht wurden.

**Noch ein Hinweis:** Das Präfix der Taglibs ist NICHT direkt
entscheidend. Man könnte auch ein <template:abctaglib /> innerhalb von
<template:anytaglib> verwenden, wenn man es darin bekannt gemacht hat.
Die einzige Voraussetzung ist, dass diese Taglib im Template selber
nicht bekannt ist, oder nach "anytaglib" hinzugefügt wurde.