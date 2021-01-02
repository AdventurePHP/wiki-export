Netbeans-Nutzer können sich die Arbeit mit dem APF etwas erleichtern,
indem sie folgendes Paket installieren.

Netbeans bietet in html Dateien eine sogenannte "Palette" an, von
welcher man sich Codeschnipsel, wie z.b. Formularelemente, per Drag&Drop
ins Editorfenster ziehen kann. Wir haben uns die Arbeit gemacht und alle
wichtigen APF-Taglibs in 4 Kategorien in diese Palette eingearbeitet,
somit muss man nicht mehr alle Taglibs auswendig kennen. Sollten zu
bestimmten Taglibs zusätzliche \*:addtaglibs für die Verwendung nötig
sein, wurden diese mit in den Codeschnipsel der Palette integriert.

Die Palette mit integrierter APF-Palette sieht in etwa so aus:

[Datei:Apf_code_completion_palette_5.png](/Datei:Apf_code_completion_palette_5.png "wikilink")

Die Code-Snippet-Vorschläge werden durch die Tasten-Kombination STRG +
SPACE aktiviert:

**Beispiel 1:**

[Datei:Apf_code_completion_palette.png](/Datei:Apf_code_completion_palette.png "wikilink")

**Beispiel 2:**

[Datei:Apf_code_completion_palette_4.png](/Datei:Apf_code_completion_palette_4.png "wikilink")

Mithilfe der Exportfunktion von Netbeans haben wir ein Paket erstellt,
welches ihr einfach importieren müsst. Dazu laded euch das Paket
[HIER](http://media.adventure-php-framework.org/netbeans/Netbeans-APF-Palette.zip)
runter, und befolgt folgende Schritte:

1.  Backup der Netbeans Optionen anlegen (man weis ja nie, auch wenn
    theoretisch nur die Palette überschrieben werden sollte, bietet sich
    ein Backup vorher an. Vorsicht mit bereits erstellten Einträgen in
    der Palette, diese werden überschrieben!)
    \[Tools-&gt;Options-&gt;(links unten)Export; am besten Haken bei
    "all" setzen und Dateipfad wählen\]
2.  Importieren des heruntergeladenen Pakets (nicht entpacken!!)
    \[Tools-&gt;Options-&gt;(links unten)Import; Datei auswählen,
    entsprechende Haken setzen\]
3.  Warnhinweise bestätigen, NB muss nicht neu gestartet werden für
    diese Änderung, auch wenn das Angeboten wird.
4.  Falls die Palette nicht standartmäßig eingeblendet wird, am rechten
    Rand suchen, andernfalls oben auf "Window" -&gt; "Palette" klicken.

Viel Spass damit wünschen Screeze und das APF-Team