Die mit dem Release 1.11 ausgelieferten Formular-Taglibs wurden
hinsichtlich Funktion und Aufbau grundlegend geändert. Details können
dem Artikel [Migration von 1.10 auf
1.11](http://adventure-php-framework.org/Seite/117-Migration-von-1-10-auf-1-11)
entnommen werden. Sofern in Produktiven Umgebungen die bisherige
Formular-Implementierung weiter verwendet werden soll, gibt es eine
einfache Möglichkeit, diese mit dem neuen Release zu nutzen.

Hierzu ist folgendes notwendig:

1.  Herunterladen eines 1.10-Releases (z.B.
    *apf-codepack-1.10-2009-09-04-1048-php5.tar.gz*).
2.  Löschen des Ordners /tools/form aus dem Release-Pack des
    1.11er-Release.
3.  Kopieren des Ordners /tools/form aus 1) in das Ziel-Release.

Bitte beachten Sie, dass es nicht möglich ist, die alten und neuen
Formular-Taglibs gleichzeitig zu verwenden. Auch sollten die alten
Taglibs nicht länger als nötig genutzt werden, da diese mit 1.11 nicht
mehr supportet werden.