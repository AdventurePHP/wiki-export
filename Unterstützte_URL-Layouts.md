## Frage

Diese Monster-URLs (*Anm. d. Red.: gemeint sind die Rewrite-URLs, die
bei einer großen Zahl an dynamischen Parametern beliebig lang werden
können.*) sind doch der absolute Horror für Besucher und Google! Ich
habe noch keine Seite gesehen, wo solche übertriebenen URLs benutzt
werden. Und genau das ist einer der größten Gründe gegen das APF,
zumindest für mich.

## Antwort

Das APF unterstützt out-of-the-box mehrere URL-Layouts: das
Standard-Layout über Request-Parameter (*?foo=bar&baz=123&...*) und
Rewrite-URLs (*/foo/bar/baz/123*). Die URL-Layouts sind jedoch für die
Applikation transparent gehalten, sprich werden von
[Eingabe-Filtern](http://adventure-php-framework.org/Seite/087-Filter)
in eine jeweils normalisierte Version übersetzt. In der ersten Variante
ist das nicht notwendig, das erledigt der Apache für dich, in der
zweiten Variante nutzt der relevante Eingabe-Filter die Semantik, dass
jeder Parameter und jeder Wert mit einem "/" getrennt sind und übersetzt
diese in ein Array aus Schlüsseln und Werten zurück.

Das sind Mechanismen, die man nutzen kann - und die in den meisten
Fällen auch völlig ausreichend sind -, man aber nicht nutzen muss. Du
kannst im APF ohne weiteres eigene Input-Filter definieren, die ein
anderes Layout in das gewünschte Format zurückübersetzen. Vorteil ist
hier ganz klar die Transparenz zur Applikation, da diese jeweils in
einem Standard-Modus läuft, nur von einem eigenen URL-Layout - sagen wir
- "verpackt" wird.

Im Bereich des
[Front-Controllers](http://adventure-php-framework.org/Seite/012-Frontcontroller)
kann der Eingabe-Filter auch Action-Anweisungen in der URL decodieren
und automatisch dem Front-Controller zur Verfügung stellen.