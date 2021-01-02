Um den Einstieg zu erleichtern, findet Sie auf dieser Seite ein
copy&paste-Rezept für die Einbindung des unter
[1](http://adventure-php-framework.org/Seite/035-Kontaktformular-Tutorial)
beschriebenen Kontaktformulars:

## Herunterladen und entpacken eines Releases

``` bash
[root@centos53 apftut]# pwd
/var/www/html/apftut
[root@centos53 apftut]# wget http://files.adventure-php-framework.org/***/download/apf-codepack-***-php5.tar.bz2
[root@centos53 apftut]# tar -xjf apf-codepack-***-php5.tar.bz2
[root@centos53 apftut]# ll
total 308
-rw-r--r--  1 root root 251079 Jan 13 22:38 apf-codepack-***-php5.tar.bz2
drwxr-xr-x 16  500  513   4096 Jan 13 22:25 APF
[root@centos53 apftut]# ll APF
drwxr-xr-x 16  500  513   4096 Jan 13 22:25 core
drwxr-xr-x 11  500  513   4096 Jan 13 22:25 extensions
-rwxr-xr-x  1  500  513   7802 Jan  9 22:16 lgpl-3.0.txt
drwxr-xr-x 11  500  513   4096 Jan 13 22:25 modules
drwxr-xr-x 15  500  513   4096 Jan 13 22:25 tools
```

## Herunterladen der Beispiel-Konfigurationsdateien

``` bash
[root@centos53 APF]# pwd
/var/www/html/apftut/APF
[root@centos53 APF]# mkdir config
[root@centos53 APF]# cd config/
[root@centos53 config]# wget http://files.adventure-php-framework.org/***/download/apf-configpack-***-noarch.tar.bz2
[root@centos53 config]# tar -xjf apf-configpack-***-noarch.tar.bz2
```

## Erstellen eines Start-Templates

``` bash
[root@centos53 APF]# pwd
/var/www/html/apftut/APF
[root@centos53 APF]# ll
total 312
drwxr-xr-x  5 root root   4096 Apr 12 15:57 config
drwxr-xr-x 16  500  513   4096 Jan 13 22:25 core
drwxr-xr-x 16  500  513   4096 Jan 13 22:25 extensions
-rwxr-xr-x  1  500  513   7802 Jan  9 22:16 lgpl-3.0.txt
drwxr-xr-x 11  500  513   4096 Jan 13 22:25 modules
drwxr-xr-x 15  500  513   4096 Jan 13 22:25 tools
[root@centos53 APF]# mkdir -p tut/site/pres/templates/ tut/site/pres/controller/
[root@centos53 APF]# vim tut/site/pres/templates/start.html
[root@centos53 APF]# cat tut/site/pres/templates/start.html
Hallo Welt!
```

## Erstellen einer Boostrap-Datei

In der Boostrap-Datei wird das zuvor erstellte Template geladen uns
ausgegeben. Dies passiert wie folgt:

``` bash
[root@centos53 APF]# cd ..
[root@centos53 apftut]# pwd
/var/www/html/apftut
[root@centos53 apftut]# vim index.php
[root@centos53 apftut]# cat index.php
<?php
include('./APF/core/bootstrap.php');

use APF\core\singleton\Singleton;
use APF\core\frontcontroller\Frontcontroller;

$fC = &Singleton::getInstance('APF\core\frontcontroller\Frontcontroller');
$fC->setContext('dev');

echo $fC->start('APF\tut\site\pres\templates', 'start');
```

Anschließend kann die index.php im Browser aufgerufen werden und es
sollte ein

``` html4strict
Hallo Welt!
```

erscheinen.

## Konfigurieren des Kontaktformulars

Das Kontaktformular benötigt folgende Konfigurationen:

-   /APF/config/modules/contact/{CONTEXT}/{ENVIRONMENT}_recipients.ini:
    Konfiguration der Empfänger
-   /APF/config/modules/contact/{CONTEXT}/{ENVIRONMENT}_language.ini:
    Sprachabhängige Texte
-   /APF/config/modules/contact/{CONTEXT}/{ENVIRONMENT}_mail_templates.ini:
    Konfiguration der E-Mail-Templates

Um die Inbetriebnahme möglichst kurz zu gestalten, bedienen wir uns
einfach der Beispiel-Konfigurationsdateien, in dem wir diese in den
richtigen Ordner kopieren und korrekt benennen. In der index.php wurde
der Context auf *dev* eingesetellt, das Environment wurde nicht
verändert. Diese besitzt damit den Wert *DEFAULT*. Es sind also folgende
Schritte notwendig:

``` bash
[root@centos53 config]# pwd
/var/www/html/apftut/APF/config
[root@centos53 config]# mkdir -p modules/contact/dev/
[root@centos53 config]# cp modules/contact/EXAMPLE_recipients.ini modules/contact/dev/DEFAULT_recipients.ini
[root@centos53 config]# cp modules/contact/EXAMPLE_language.ini modules/contact/dev/DEFAULT_language.ini
[root@centos53 config]# cp modules/contact/EXAMPLE_mail_templates.ini modules/contact/dev/DEFAULT_mail_templates.ini
```

## Inbetriebnahme / Einbindung des Formulars

Nun können wir das Kontaktformular in das oben angelegte Template - und
damit in jedes APF-Template - per Tag einbinden. Hierzu muss der Inhalt
von *start.html* wie folgt aussehen:

``` bash
[root@centos53 templates]# pwd
/var/www/html/apftut/APF/tut/site/pres/templates
[root@centos53 templates]# vim start.html
[root@centos53 templates]# cat start.html
<core:importdesign namespace="APF\modules\contact\pres\templates" template="contact" />
```

Bei Aufruf der index.php im Browser erscheint nun das Formular. Wie dir
sicher schnell auffällt, fehlt das CAPTCHA-Bild. Hintergrund dafür ist,
dass das Bild dynamisch erzeugt und mit Hilfe einer
Frontcontroller-Action ausgeliefert wird. Die Konfiguration desselben
erfordert folgende weitere Schritte:

## Konfiguration der CAPTCHA-Bild-Action

Damit das Bild ausgeliefert wird, muss die Action, die dazu genutzt wird
noch für den Frontcontroller konfiguriert werden. Dazu kopieren wir auch
dieses mal einfach die Beispiel-Konfigurationsdateien:

``` bash
[root@centos53 config]# pwd
/var/www/html/apftut/APF/config
[root@centos53 config]# mkdir -p modules/captcha/biz/actions/dev/
[root@centos53 config]# cp modules/captcha/biz/actions/EXAMPLE_actionconfig.ini modules/captcha/biz/actions/dev/DEFAULT_actionconfig.ini
```

Nach einem F5 wird nun auch das CAPTCHA-Bild angezeigt.

## Literatur

Folgende Stellen im Tutorial sollten zum weiteren Verständnis helfen:

-   <http://adventure-php-framework.org/Seite/033-Konfiguration#Chapter-2-Bennenung-Pfade-und-Dateien>
-   <http://adventure-php-framework.org/Seite/098-Pagecontroller>
-   <http://adventure-php-framework.org/Seite/012-Frontcontroller>
-   <http://adventure-php-framework.org/Seite/094-Module-CAPTCHA-Taglib>

## Einstieg in das APF

Für den Einstieg in das Framework empfiehlt es sich, das
demopack-Release herunter zu laden. Dieses kann sehr einfach als
"Entwicklungsumgebung" genutzt werden. Zudem ist der Thread [Hilfe für
APF-Neuling](http://forum.adventure-php-framework.org/viewtopic.php?f=1&t=80)
hier im Forum sehr hilfreich, da gerade auf das Thema Konfiguration
nochmal in der Tiefe eingegangen wird.