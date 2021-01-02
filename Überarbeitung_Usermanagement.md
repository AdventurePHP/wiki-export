<div style="background-color: #FFAA00;">


Diese Seite befindet sich noch im Aufbau! Anregungen, Ideen und
Vorschläge können im
[Forum](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=673)
besprochen werden.

</div>

------------------------------------------------------------------------

Diese Seite beschreibt eine für das Release 1.14 geplante Überarbeitung
des Usermanagements (Stand 04.04.2011). Die neuen und zu überarbeitenden
Funktionen beziehen sich auf mehrere Forumsbeiträge [Umgt - Hardcoded
Salt](http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=581),
[Usermanagement](http://forum.adventure-php-framework.org/de/viewtopic.php?f=5&t=97),
[Login eines Benutzers mit dem Usermanagement
Modul](http://forum.adventure-php-framework.org/de/viewtopic.php?f=6&t=134)

## Einleitung

Diese Seite beschreibt ein Proposal zur Überarbeitung des Usermanagement
Moduls. Ziel ist es, dass Usermanagement mit einer Login- und
Registrierungskomponente zu erweitern. Außerdem soll standardmäßig ein
Salt beim Hashen eines Passwortes hinzugefügt werden, um die Sicherheit
zu erhöhen. Die Zuordnung von z.B. Benutzer zu Gruppen soll überarbeitet
werden, siehe [GenericORRelationMapper::createAssociations
erweitern](http://forum.adventure-php-framework.org/de/viewtopic.php?f=7&t=663#p6684)

## Grundsätze

-   Das Usermanagement Modul ist ein standardmäßig mitgeliefertes Modul
    des APF, welches zur Verwaltung von Benutzern, Gruppen, Rollen,
    Berechtigungen usw. eingesetzt werden kann
-   Das Hashen eines Passwortes erfolgt über einen PasswordHashProvider.
    Um mit älteren Versionen kompatibel zu bleiben, soll es die
    Möglichkeit geben, nachträglich noch PasswordHashProvider
    hinzuzufügen.

## Offene Punkte

-   Das Usermanagement Modul soll einfache Templates und Methoden für
    Login und Registrierung bereitstellen
-   Die assign\* Methoden sollen auf nicht gespeicherte Objekte
    reagieren, da sonst inkorrekte Werte in die Datenbank eingetragen
    werden
-   Dokumentation und Tutorials sollten überprüft und angepasst werden