## Frage

Wie kann ich mit dem APF Module sauber entkoppeln?

## Antwort

Für die Entkopplung von Modulen eignet sich die URL am besten. Um die
Arbeit mit der URL zu unterstützen, beinhaltet das APF die beiden Tools
[LinkHandler](http://adventure-php-framework.org/Seite/036-Links) und
[FrontcontrollerLinkHandler](http://adventure-php-framework.org/Seite/012-Frontcontroller#Chapter-2-4-Generierung-von-Links)
(für die Nutzung des
[Front-Controller](http://adventure-php-framework.org/Seite/012-Frontcontroller)).
Diese generieren eine URL auf Basis einer gegebenen URL und manipulieren
exakt die gewünschten Parameter.

Zur Verdeutlichung wurde eine Demo-Applikation geschrieben, die 3
Teaser-Boxen untereinander anzeigt. In diesen werden pro Seite eine
definierte Anzahl an Listen-Elementen angezeigt, die je nach gewünschter
Seite wechseln. Die Entkopplung der drei Boxen basiert auf der
Verwendung des LinkHandler. Dieser manipuliert die aktuelle URL so, dass
die gewünschten Paging-Parameter gegen diejenigen ausgetauscht werden,
die für die gewünschte Seite relevant sind.

Noch ein Wort zur Implementierung: da die Funktion der 3 Teaser-Boxen
nahezu gleich ist, habe ich diese im teaser_base_controller
zusammengefasst und nur den variablen Anteil in den einzelnen
Controllern definiert. Bei "echten" drei Modulen würde sich der Code
natürlich mehr unterscheiden, der Effekt ist jedoch der gleiche.
Templates gibt es 3 verschiedene - die auch noch auslagerbaren Code
enthalten, aber das ist zu Demo-Zwecken nicht relevant. Die Aufteilung
in die Ordner one, two und three ist nicht zwingend, sondern jederzeit
anpassbar. Ich hab das nur aus Gründen der Übersichtlichkeit so gewählt.
Verschiedene Module würden üblicherweise ja auch in unterschiedliche
Namespaces abgelegt werden.

Das Beispiel kann unter
<http://media.adventure-php-framework.org/support/teaser-sample-url-decoupling.tar.gz>
heruntergeladen werden.