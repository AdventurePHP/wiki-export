## Einleitung

Die gesamte Entwicklung des APF steht seit dem Release der Version 1.7
beta über ein öffentliches Subversion-Repository zur Verfügung. Der
aktuelle Entwicklungsstand kann darüber einfach und bequem bezogen
werden und sämtliche Updates werden übersichtlich und ausführlich
gelistet.

## Vorrausetzung

Das SVN wird über sourceforge.net bereitgestellt. Um aktiv an der
Entwicklung teilnehmen zu können, ist ein Benutzer-Konto bei Sourceforge
notwendig. Die Registrierung geht schnell und einfach und ist kostenlos.
[Hier gehts zur Registrierung auf
Sourceforge.net](https://sourceforge.net/user/registration) Nachdem die
Registrierung bestätigt wurde, musst du als User von Dr. E.
freigeschaltet werden. Erst danach kannst du deine Entwicklungen
beisteuern.

## Die Software

Als SVN-Client bietet sich TortoiseSVN an. Den Client erhälst du hier:
[TortoiseSVN Downloadseite](http://tortoisesvn.net/downloads.html)
Nachdem Download muss die Software installiert werden. Es bietet sich
an, die Menüerweiterungen mit zu installieren. Dadurch hast du an jedem
beliebigen Ort auf deinem PC die Möglichkeit, Dateien aus dem SVN heraus
zu ziehen ("Checkout").

[Datei:Tortoise-SVN_Menuerweiterung.jpg](/Datei:Tortoise-SVN_Menuerweiterung.jpg "wikilink")

## Checkout

Um nun eine Datei des APF zu aktualisieren, muss diese vorher
ausgecheckt werden. Erstelle dir dazu ein neues Verzeichnis auf deinem
PC, welches dir als zukünftiges Arbeitsverzeichnis dienen soll. In
diesem Verzeichnis machst du nun an einer beliebigen Stelle einen
Rechtsklick und das oben gezeigte Kontextmenü sollte in ähnlicher Form
erscheinen. Wähle dort dann *SVN Checkout...* Es öffnet sich
nachfolgendes Fenster:

[Datei:Tortoise-SVN_Checkout.jpg‎](/Datei:Tortoise-SVN_Checkout.jpg‎ "wikilink")

**URL of repository:** Gibt an, von wo ausgecheckt werden soll. Über den
Button "..." wird der Repoitory Browser geöffnet. Das Fenster entspricht
einem Dateibrowser.

Noch eine kleiner Hinweis bezüglich der APF-SVN-Struktur: Die
Dokumentationsseite, Medien sowie die aktuelle Entwicklung und ihre
Vorgänger sind im SVN verwaltet. Die Entwicklungen in den
unterschiedlichen Versionen befinden sich im Unterverzeichnis
*branches*.

Der aktuelle Entwicklungsstand lässt sich also unter folgender URL
finden:
*<https://adventurephpfra.svn.sourceforge.net/svnroot/adventurephpfra/branches/php5/1.16>*
Das Fenster könnte also wie folgt aussehen:

[Datei:Tortoise-SVN_Repository-Browser.jpg‎](/Datei:Tortoise-SVN_Repository-Browser.jpg‎ "wikilink")

Tipp: Möchtest du nicht alles sondern nur einen Teil herunter ziehen,
klicke dich weiter in der Dateistruktur durch. Was du im rechten Feld
selektierst und über "OK" bestätigst, wird am Ende herunter geladen.

**Checkout Directory**: Gibt an, wohin die Dateien auf deinem Computer
gespeichert werden sollen. Als Standard wird dort automatisch dein
Ordner, indem du den Checkout gestartet hast, vorgewählt. Soll es
woanders hingehen, einfach über "..." das Ziel wählen.

**Checkout Depth**: Als Checkout Tiefe wählen wir *fully recursive* um
komplett alles herunter zu ziehen.

**Revision:** Keine Ahnung, was das genau ist aber HEAD Revision hat
sich bei mir immer bewährt.

Nach einem Klick auf "OK" öffnet sich ein fenster, indem du den
Download-Vorgang beobachten kannst. Ist alles fertig, kannst du das
Fenster bedenkelos schliessen. Du hast nun den letzten Entwicklungsstand
auf deinem PC vorliegen!

## Commit

Möchtest du Dateien im SVN aktualisieren, musst du diese einchecken.
Über das Tortoise SVN heisst diese Funktion *Commit*.

Um einen *Commit* oder *Checkin* zu starten, musst du vorher deine
aktualisierten Dateien in dein Arbeitsverzeichnis kopieren, solltest du
diese nicht bereits aktualisiert haben.

Die Übersicht in deinem Arbeitsordner sollten danach so oder so ähnlich
aussehne (ja nach deinen Änderungen):

[Tortoise-SVN_neue-Version-erkannt.jpg](/Tortoise-SVN_neue-Version-erkannt.jpg "wikilink")

Neue oder aktualisierte Dateien werden mit einem roten Ausrufezeichen
markiert. Alle Dateien, welche noch den selbsen Stand haben, werden über
einen grünen Haken markiert.

Anschliessend kannst du mit einem Rechtsklick in deinen Arbeitsordner
auf die aktualiserte Datei einen Checkin starten. Wähle dazu du Option
**SVM Commit...** aus dem Kontextmenü.

[Datei:Tortoise-SVN_SVN-Commit.jpg](/Datei:Tortoise-SVN_SVN-Commit.jpg "wikilink")

Es öffnet sich ein neues Fenster:

[Datei:Tortoise-SVN_SVN-Commit-To.jpg](/Datei:Tortoise-SVN_SVN-Commit-To.jpg "wikilink")

Folgende Einstellmöglichkeiten bietet das Fenster:

**Commit to:** Der Pafd zum Online-Verzeichnis, indem die Datei/en
aktualisert werden sollen. Dies wird automatisch durch den Checkout
ausgefüllt

**Message:** Hier kannst du eine Beschreibung deiner Änderungen
eingeben. Diese werden in der SVN-Übersicht sowie in den Detailansichten
angezeigt und jeder Entwickler kann somit die Änderungen leichter
nachvollziehen. Über *Recent Messages* kannst du vorherige Änderungen
und deren Revisionen einsehen.

**Changes made:** Hier werden alle Dateien markiert, an denen eine
Änderung festgestellt wurde und welche dann auf den SVN-Server geladen
werden.

Über einen Klick auf **OK** startest du den *Commit*. Es öffnet sich
wiederrum ein neues Fenster mit Statusinformationen üder deinen checkin:

[Datei:Tortoise-SVN_SVN-Commit-Finished.jpg](/Datei:Tortoise-SVN_SVN-Commit-Finished.jpg "wikilink")

Am Ende siehst du, welche Revisions-Nummer neu erzeugt wurde. Dies
entspricht dann dem neuesten/letzten Stand der Software.

Viel Erfolg und Spass beim Nutzen des APF-SVN!