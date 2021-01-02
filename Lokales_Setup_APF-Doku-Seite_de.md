Die folgende Anleitung zeigt, wie die offizielle APF-Webseite auf einer
lokalen Entwicklungsmschine installiert werden kann.

## GIT Checkout

Die Quellen der APF-Webseite werden unter
[GitHub.com/docs](https://www.github.com/AdventurePHP/docs) verwaltet.
Zusätzlich dazu benötigen Sie die Inhalte des
[code](https://www.github.com/AdventurePHP/docs)-Repositories in der
gewünschten Version. Für letztere ist bereits eine
Submodule-Konfiguration auf den aktuellen Entwicklungszweig vorhanden,
der lokal nur noch initialisiert werden muss.

Führen Sie zunächst ein

``` bash
mkdir -p /var/www/html/docu-page && cd /var/www/html/docu-page
git clone https://www.github.com/AdventurePHP/docs.git .
```

aus. Sie haben nun die Quellen der APF-Webseite zur Verfügung.

In einem weiteren Schritt ist muss nun das definierte Submodule *code*
wie folgt initialisiert werden:

``` bash
git submodule init
```

Sofern es für das **code**-Repository bereits eine lokale Arbeitskopie
gibt, wird git versuchen, diese als Remote-Repository zu nutzen. Um dies
zu umgehen, können Sie im Ordner */var/www/html/docu-page/APF* das
Kommando

``` bash
git remote set-url origin https://github.com/AdventurePHP/code.git
```

ausführen.

Nun sind auch die Quellen des Frameworks lokal vorhanden und Sie können
mit den folgenden Kapiteln fortfahren.

## Einrichtung der VHOSTs

Für den Betrieb der Webseite ist ein virtueller Host erforderlich:

apf.org (o.ä.)

Die Domain *apf.org* kann beliebig angepasst werden.

Die folgende Code-Box zeigt ein Beispiel für die notwendigen VHOSTs
(Checkout-Verzeichnis: */var/www/html/docu-page*):

``` apache
<VirtualHost *:80>
    DocumentRoot /var/www/html/docu-page
    ServerName apf.org
    AllowEncodedSlashes On
    ...
</VirtualHost>
```

## Anpassung .htaccess

Die SVN-Quellen liefern einen Wizzard zur lokalen Einrichtung der
APF-Webseite mit. Um diesen unter *<http://apf.org/setup_wizard.php>*
aufgerufen werden kann, ist zumächst eine Anpassung der
*.htaccess*-Datei notwendig. Entfernen Sie bitte die folgenden Zeilen:

``` apache
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]
```

Anschließend legen Sie bitte den Ordner */var/www/html/docu-page/files*
an. Dieser wird von der Applikation als Release-Ordner interpretiert,
unter dem die erzeugten APF-Releases liegen.

## Ausführen des Wizzard

Im Anschluss an die Vorarbeiten können Sie nun die einzelnen Schritte
des Wizzard durchlaufen.

Die einzelnen Schritte sind bewusst getrennt gehalten, damit Sie
nachträglich auch eigenständig ausgeführt werden können. Dies ist
insbesondere für die Pflege des Such-Index wichtig.

### Anpassung der index.php

Die Konfiguration der *index.php* ist von Ihrer lokalen Umgebung
abhängig. Mit dem ersten Wizzard
*<http://apf.org/setup_wizzard.php?page=index>* können Sie diese
anpassen.

### Datenbank-Konfiguration

Im Anschluss können Sie die Datenbank-Konfiguration vornehmen. Der
Wizzard *<http://apf.org/setup_wizard.php?page=database>* geht davon
aus, dass alle benötigten Verbindungen über den selben Benutzer verfügen
Sollte dies für Ihre lokale Umgebung nicht zutreffen, passen Sie die
Konfigurations-Datei bitte nach dem Ausführen des Wizzards manuell an.

Weitere Instruktionen entnehmen Sie bitte dem Wizzard.

### Datenbank-Initialisierung

Um die Anwendung lokal betreiben zu können, muss die gewählte Datenbank
bzw. die gewählten Datenbanken initialisiert werden. Der Wizzard unter
*<http://apf.org/setup_wizard.php?page=setup>* lässt sich dabei auch
mehrmals ausführen und Sie können entweder alle Datenbanken zusammen
oder mehrere einzeln initialisieren.

Mehrmaliges Ausführen führt dabei nicht zu Datenverlust, da der Wizzard
keine Daten löscht und die Tabellen nur anlegt, wenn diese noch nicht
erstellt wurden.

Weitere Instruktionen entnehmen Sie bitte dem Wizzard.

### Erstellen des Such-Index

Der Such-Index erfüllt zwei Aufgaben: URL-Generierung und Basis für die
Suche. Die URL-Generierung nutzt dabei die Artikel-Liste innerhalb des
Suche-Index.

Führen Sie den Wizzard *<http://apf.org/setup_wizard.php?page=search>*
immer dann aus, wenn Sie einen neuen Artikel angelegt haben oder den
Such-Index aktualisieren wollen.

Weitere Instruktionen entnehmen Sie bitte dem Wizzard. <languages />