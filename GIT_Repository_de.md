## Einleitung

Die GIT Repositories werden auf GitHub gehostet und finden sich in der
Organisation [github.com/AdventurePHP](https://github.com/AdventurePHP).

## Mitwirken

Das APF-Team freut sich jedezeit über Beiträge zu Code, Dokumentation
und Ideen für die Weiterentwicklung. Um deinen Beitrag einzusenden
kannst du folgende Wege nutzen:

-   Pull-Request deines Forks von
    [github.com/AdventurePHP](https://github.com/AdventurePHP).
-   Neuer Eintrag im
    [Tracker](http://tracker.adventure-php-framework.org/bug_report_page.php).
-   Post im [Forum](http://forum.adventure-php-framework.org).
-   E-Mail an das
    [APF-Team](http://adventure-php-framework.org/Seite/149-Team).

## Repositories

Unter [github.com/AdventurePHP](https://github.com/AdventurePHP)
befinden sich aktuell drei GIT Repositories. Diese werden wie folgt
verwendet:

-   [code](https://github.com/AdventurePHP/code): Beinhaltet den Code
    des Frameworks und Beispiel-Konfigurations-Dateien für die
    entsprechenden Komponenten
-   [config](https://github.com/AdventurePHP/config): Beinhaltet
    Beispiel-Konfigurationen für die im **code**-Repository verwalteten
    Komponenten.
-   [examples](https://github.com/AdventurePHP/examples): Beinhaltet
    verschiedene Beispiel-Implementierungen die zu Entwicklung und
    Demonstration genutzt werden können (v.a.
    [Sandbox](http://adventure-php-framework.org/Seite/074-Sandbox)).
-   [docs](https://github.com/AdventurePHP/docs): Hier werden die
    Quellen der APF-Webseite und der offizielle Dokumentation verwaltet.
-   [tools](https://github.com/AdventurePHP/tools): In diesem Repository
    finden Sie die Build-Skripte für APF-Releases und weitere Tools.

## Branches

### Übersicht

Die Repositories

-   **code**
-   **config**
-   **examples**

besitzten *parallel* (wichtig für das Erstellen der Release-Artefakte!)
folgende Branches:

| Name                                           | Beschreibung                                                                                                                                                                                                                                                                            |
|------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **master**                                     | In diesem Branch findet die Entwicklung der nächsten Release-Version statt. Bestehende Feature-Branches werden nach der Fertigstellung der neuen Funktionalität dorthin zurückgeführt.                                                                                                  |
| **release-\*** (z.B. *release-2.0*)            | Release-Branches dienen zur Auslieferung eines Releases und der Pflege der Code-Linie mit ggf. weiteren Service-Packs oder Hotfix-Releases. Die Änderungen werden in den *master* überführt.                                                                                            |
| **feature-\*** (z.B. *feature-2.0-namespaces*) | Feature-Branches dienen dazu, ein Feature für ein kommende Version bereits vor der Freigabe des *master*-Branches für die Release-Entwicklung zu starten. Ein weiterer Anwendungsfall ist die Entwicklung eines Features oder Proposals um Feedback von anderen Entwicklern einzuholen. |

Das Branching-Konzept ist an das im Artikel [A successful Git branching
model](http://nvie.com/posts/a-successful-git-branching-model/)
beschriebene Vorgehen angelehnt. Die oben beschriebene Liste zeigt
jedoch nur diejenigen Branches auf, die auf GitHub öffentlich verfügbar
sind. Jeder Entwickler besitzt zudem eigene, lokale Branches um die
Entwicklung von neuen Funktionen vorzunehmen.

### Existierende Branches

Die folgende Liste zeigt die existierenden Branches und deren Bedeutung
bzw. Einsatz-Zweck:

| Name                   | Beschreibung                                                                       |
|------------------------|------------------------------------------------------------------------------------|
| master                 | Feature-Entwickung für Release 2.1.                                                |
| release-1.10           | Release- und Bugfixing-Stand für Release 1.10.                                     |
| release-1.11           | Release- und Bugfixing-Stand für Release 1.11.                                     |
| release-1.12           | Release- und Bugfixing-Stand für Release 1.12.                                     |
| release-1.13           | Release- und Bugfixing-Stand für Release 1.13.                                     |
| release-1.14           | Release- und Bugfixing-Stand für Release 1.14.                                     |
| release-1.15           | Release- und Bugfixing-Stand für Release 1.15.                                     |
| release-1.16           | Release- und Bugfixing-Stand für Release 1.16.                                     |
| release-1.17           | Release- und Bugfixing-Stand für Release 1.17.                                     |
| release-2.0            | Release- und Bugfixing-Stand für Release 2.0/2.0.X.                                |
| release-2.1            | Release- und Bugfixing-Stand für Release 2.1.                                      |
| feature-1.13-form      | Feature-Branch für Release 1.13.                                                   |
| feature-2.0-gorm       | Feature-Branch für den GORM in Release 2.0.                                        |
| feature-2.0-namespaces | Feature-Branch für die Einführung von Namespaces in Release 2.0.                   |
| feature-2.2-parser-poc | Feature-Branch für die Einführung des neuen APF-Parsers Namespaces in Release 2.2. |

## Release-Artefakte

Aus den Branches

-   **code**
-   **config**
-   **examples**

entstehen folgende Release-Artefakte:

| Name                                  | Beschreibung                                                                                                                                        |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| apf-codepack-<version>-php5.\*        | Beinhaltet die Code-Basis des APF inkl. notwendiger Migrations-Skripten (falls für den Umstieg auf diese Version vorhanden).                        |
| apf-configpack-<version>-noarch.\*    | Beinhaltet zur Version passende Beispiel-Konfigurations-Dateien.                                                                                    |
| apf-demopack-<version>-php5.\*        | Beinhaltet die [Sandbox](http://adventure-php-framework.org/Seite/074-Sandbox) als Beispiel-Anwendung und Entwicklungs-Plattform für eigene Module. |
| apf-calc-example-<version>-php5.\*    | Liefert eine Taschenrechner-Implementierung auf Basis des APF.                                                                                      |
| apf-modules-example-<version>-php5.\* | Zeigt wie sich dynamische Module für eine GUI realisieren lassen.                                                                                   |
| apf-vbc-example-<version>-php5.\*     | Code zum Artikel [View-based Caching](http://adventure-php-framework.org/Seite/086-View-based-caching-ein-HOWTO).                                   |

## Arbeiten mit der GIT-Version

Änderungen und Bugfixes von Issues im
[Tracker](http://tracker.adventure-php-framework.org/roadmap_page.php)
werden jeweils nach der Bearbeitung in den Branches *master* oder
*release-\** auf GitHub zur Verfügung gestellt. Sofern Sie diese sofort
einsetzen möchten, besteht jederzeit die Möglichkeit, die publizierten
Änderungen in Ihrem Projekt einzusetzen.

Die folgenden Kapitel zeigen Ihnen, wie Sie Ihre lokale Version
aktualisieren können.

### Snapshot

Die einfachste Option die neu verfügbaren Änderungen zu nutzen sind
Snapshot-Versionen. Sofern Sie keine Historie der Änderungen benötigen,
können Sie die jeweiligs aktuellste Version auf der Seite [Get
Latest](http://adventure-php-framework.org/) herunter laden. Die
Snapshots werden stündlich aktualisiert.

### Clone

Mit einem Clone eines APF-Repositories (üblicherweise **code**) erhalten
Sie die vollständige Historie der Änderungen und können diese als
Grundlage für den Einsatz des APF in Ihrem Projekt nutzen.

Um diese Option zu nutzen muss ihr Projekt entsprechend vorbereitet
sein. Voraussetzungen sind:

-   Die APF-Code-Dateien liegen in einem eigenen Ordner und sind von
    Ihrer Applikation komplett getrennt. Beispiel: die Dateien Ihrer
    Applikation sind unter *APP* zu finden, die APF-Quellen unter *APF*
    parallel dazu. Hinweise dazu finden Sie im Kapitel [Anpassung des
    Basis-Pfads](http://adventure-php-framework.org/Seite/154-Laden-von-Klassen#Chapter-4-1-Anpassung-des-Basis-Pfads)
    bzw. im Tutorial [Erstellen einer
    Webseite](http://adventure-php-framework.org/Seite/048-Webseite-erstellen#Chapter-3-2-Erstellen-der-Applikations-Struktur).
-   Die Konfigurations-Dateien für APF-Komponenten sind unter einem
    eigenen Pfad abgelegt, der unabhängig vom Ordner *APF* ist.
    Beispiel: die APF-Quellen sind unter *APF* zu finden, die
    zugehörigen Konfigurationen unter *config/APF*. Hinweise dazu finden
    Sie im Kapitel [Anpassung des Basis-Pfads für
    Konfigurationen](http://adventure-php-framework.org/Seite/154-Laden-von-Klassen#Chapter-4-2-Anpassung-des-Basis-Pfads-fuer-Konfigurationen)
    bzw. im Tutorial [Erstellen einer
    Webseite](http://adventure-php-framework.org/Seite/048-Webseite-erstellen#Chapter-3-2-Erstellen-der-Applikations-Struktur).

Anschließend können Sie das
[Submodule](http://git-scm.com/docs/git-submodule)-Feature von Git
nutzen um im Ordner *APF* einen Clone des **code**-Repositories zu
erstellen:

``` bash
cd /path/to/project
mkdir APF && cd APF
git clone https://github.com/AdventurePHP/code.git .
```

<languages />