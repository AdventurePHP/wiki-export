Bei der Erstellung eines Releases müssen gemäß der Dokumentation der
Repository-Struktur ([GIT Repository](/GIT_Repository "wikilink"))
entsprechende Branches und Tags erzeugt werden. Dies wird in den
folgenden Kapiteln kurz beschrieben.

## Branching

Zunächst wird ausgehend vom aktuellen Entwicklungszweig (*master*) ein
Release-Branch erstellt. Dieser wird ab der Ausliegerung des ersten
Releases (z.B. *2.1.0*) für die Pflege des Releases genutzt.

Für die Repositories **code**, **examples** und **config** ist folgendes
auszuführen:

``` bash
git checkout -b release-2.1 master
git push origin release-2.1
```

## Tagging

Basierend auf dem jeweiligen Release-Branch werden für jede Auslieferung
Tags erstellt.

Für die Repositories **code**, **examples** und **config** ist folgendes
auszuführen:

``` bash
git tag -a 2.1 -m "Final release state for 2.1." release-2.1
git push origin 2.1
```

<languages />