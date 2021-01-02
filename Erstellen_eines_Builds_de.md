Auf dieser Seite sind die aktuell verfügbaren Build-Skripten des APF
dokumentiert. Weiter unten finden Sie eine Anleitung zur Erstellung
eines Builds.

## Vorbereitung

### Windows

Unter Windows können die Build-Skripten ausschließlich unter Cygwin
ausgeführt werden, da es sich um Bash-Skripte handelt. Bitte
installieren Sie daher folgende Komponenten:

-   Cygwin (Anleitung und Download unter
    [cygwin.org](http://cygwin.org)).
-   doxygen + graphviz (Anleitung und Download unter
    [sourceware.org/cygwinports](https://sourceware.org/cygwinports)).

### LINUX

Unter LINUX können Sie die benötigten Paketen mit dem Paket-Manager
ihrer Distribution installieren. Es werden folgende zusätzliche Pakete
benötigt:

-   Git (z.B. *git-1.9.0-1.fc20.i686* unter Fedora 20)
-   Doxygen (z.B. *doxygen-1.8.6-1.fc20.i686* unter Fedora 20)
-   Graphviz bzw. dot (z.B. *graphviz-2.34.0-8.fc20.i686* unter
    Fedora 20)

## Snapshot-Release

Wie unter \[GIT Repository\] beschrieben werden stündlich
Snapshot-Releases von dafür eingerichteten Branches erzeugt. Diese
nutzen das Skript
[create-git-snapshot.sh](https://github.com/AdventurePHP/tools/blob/master/build/snapshot/create-git-snapshot.sh)
um die Inhalte zur Veröffentlichung vorzubereiten.

Die Signatur gestaltet sich wie folgt:

``` bash
$ ./create-git-snapshot.sh
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF snapshot                                                     ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 12:12:07
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Not enough parameters. Aborting!
Usage: ./create-git-snapshot.sh <git-branch> <release-version>

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 12:12:07
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

Möchten Sie eine Snapshot-Version lokal erzeugen, können Sie den
folgenden Aufruf dazu nutzen:

``` bash
$ ./create-git-snapshot.sh master 2.1
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF snapshot                                                     ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 11:46:59
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

[INFO] Creating snapshot release for version 2.1 from branch master ...
[INFO] Using workspace /cygdrive/c/Users/Christian/Entwicklung/GIT/tools/build/snapshot/workspace to preare snapshot release ...
[INFO] Clearing workspace ...
[INFO] Fetching sources from GitHub ...
[INFO] Fetching sample config from GitHub ...
[INFO] Creating TGZ file ...
[INFO] Publishing snapshot file apf-2.1-snapshot-php5.tar.gz for version 2.1 from branch master ...

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 11:47:09
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

Beim lokalen Ausführen kann es zu folgendem Fehler bei der Publizierung
des Snapshots kommen:

``` bash
mv: cannot move `../apf-2.1-snapshot-php5.tar.gz' to `/cygdrive/***/tools/build/snapshot/../files/snapshot': No such file or directory
```

Diesen können Sie zunächst ignorieren, da die Snapshot-Datei trotzdem im
aktuellen Verzeichnis erzeugt wurde.

Bitte beachten Sie, dass Snapshot-Versionen ausschließlich von den
Quellen unter [github.com/AdventurePHP](https://github.com/AdventurePHP)
erzeugt werden können. Lokale GIT-Repositories werden nicht unterstützt.
Daher ist das Erzeugen eines Snapshot-Releases von unterwegs nicht
möglich.

## Release-Build

Release-Builds werden nach der Freigabe des Code-Standes manuell und
lokal erzeugt. Hierfür steht das Skript
[create_build.sh](https://github.com/AdventurePHP/tools/blob/master/build/release/create_build.sh)
zur Verfügung. Dieses besitzt mehrere Optionen und ermöglicht u.a. das
Erzeugen eines Releases aus einem lokalen GIT-Repository zu.

### Konfiguration

Um das Skript lokal ausführen zu können, muss eine lokale
Konfigurationsdatei angelegt werden - ein Build-Profil. Das Profil (eine
Bash-Datei) beinhaltet folgende Parameter:

| Name                       | Beschreibung                                                                                                                                                                               |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BUILD_PATH                | Der hier angegebene, voll-qualifizierte Pfad definiert, wo das Release erzeugt wird. Dies umfasst den aktuellen Workspace als auch den Ablage-Ort des fertigen Releases.                   |
| CODE_LOCAL_REPO_PATH    | Sofern ein lokales GIT-Repository für das [code-Repository](https://github.com/AdventurePHP/code) genutzt werden soll, geben Sie bitte hier den voll-qualifizierten Pfad dahin an.         |
| DOCS_LOCAL_REPO_PATH    | Sofern ein lokales GIT-Repository für das [docs-Repository](https://github.com/AdventurePHP/docs) genutzt werden soll, geben Sie bitte hier den voll-qualifizierten Pfad dahin an.         |
| CONFIG_LOCAL_REPO_PATH  | Sofern ein lokales GIT-Repository für das [config-Repository](https://github.com/AdventurePHP/config) genutzt werden soll, geben Sie bitte hier den voll-qualifizierten Pfad dahin an.     |
| EXAMPLE_LOCAL_REPO_PATH | Sofern ein lokales GIT-Repository für das [examples-Repository](https://github.com/AdventurePHP/examples) genutzt werden soll, geben Sie bitte hier den voll-qualifizierten Pfad dahin an. |

Die Konfigurations-Datei kann an einem beliebigen Ort abgelegt werden.
Sie wird später im Aufruf des Build-Skripts angegeben.

Beispiel (unter Windows/cygwin):

``` bash
$ cat apf_build.conf
# local repo path to clone for build
DOCS_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/docs
CODE_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/code
CONFIG_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/config
EXAMPLE_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/examples

# build environment
BUILD_PATH=/cygdrive/c/Users/Christian/Entwicklung/Build
```

### Parameter

Das Build-Skript besitzt folgende Parameter zur Steuerung:

<table>
<thead>
<tr class="header">
<th><p>Name</p></th>
<th><p>Beschreibung</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>-c</p></td>
<td><p>Voll-qualifizierter Name der im vorherigen Kapitel beschriebenen Konfigurations-Datei.</p></td>
</tr>
<tr class="even">
<td><p>-b</p></td>
<td><p>GIT-Branch auf Basis dessen das Release erzeugt werden soll. Diese Option wird für den Checkout der Repositories <a href="https://github.com/AdventurePHP/code">code</a>, <a href="https://github.com/AdventurePHP/config">config</a> und <a href="https://github.com/AdventurePHP/examples">examples</a> genutzt.</p></td>
</tr>
<tr class="odd">
<td><p>-v</p></td>
<td><p>Versionsnummer des Builds (z.B. <em>2.1</em>).</p></td>
</tr>
<tr class="even">
<td><p>-m (optional; Standard: <em>all</em>)</p></td>
<td><p>Gibt an, welche Teile des Builds ausgeführt werden sollen. Zu diesen zählen:</p>
<ul>
<li>apidocs: APF doxygen Dokumentation.</li>
<li>codepack: Code Release.</li>
<li>configpack: Beispiel-Konfigurations-Dateien.</li>
<li>demopack: Sandbox Release</li>
<li>examples: Implementierungs-Beispiele (vbc, modules, calc).</li>
</ul></td>
</tr>
</tbody>
</table>

Rufen Sie das Skript ohne Parameter oder mit der *-h*-Option auf wird
Ihnen die Hilfe angezeigt:

``` bash
$ ./create_build.sh -h
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF release                                                      ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 12:32:04
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Usage: create_build.sh -c <config-file> -b <git branch> -v <build-version> [-m <modules-to-build>]

Please note: <git branch> must be replaced with the name of the GIT branch and <build-version> indicates the version number in the release files.
Using the -o parameter you can create a build with the sources downloaded beforehand.
The configuration file referred to must specify the following parameters:

- CODE_LOCAL_REPO_PATH (opt)   : The source location where the APF sources are checked out on local disk.
- DOCS_LOCAL_REPO_PATH (opt)   : The source location where the APF documentation page is checked out on local disk.
- CONFIG_LOCAL_REPO_PATH (opt) : The source location where the APF sample config is checked out on local disk.
- EXAMPLE_LOCAL_REPO_PATH (opt): The source location where the APF examples are checked out on local disk.
- BUILD_PATH                   : The path where the build is created and stored.

In case the configuration defines "DOCS_LOCAL_REPO_PATH" and/or "CODE_LOCAL_REPO_PATH" and/or "CONFIG_LOCAL_REPO_PATH" and/or "EXAMPLE_LOCAL_REPO_PATH" the build script tries to use a local clone of the APF repo(s).

The list of modules (-m) can either be "all" (default) or a comma-separated list of the following items (or just one):

- apidocs   : APF doxygen documentation.
- codepack  : Code release files.
- configpack: Sample configuration files.
- demopack  : Sandbox release files.
- examples  : Implementation examples (vbc, modules, calc).

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 12:32:04
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

### Erstellen

Um ein Release-Build zu erstellen, können Sie folgende Aufrufe nutzen:

``` bash
$ for LOCAL_REPO in $(cat apf_build.conf | grep "_LOCAL_REPO_PATH" | cut -d "=" -f2); do cd $LOCAL_REPO && git pull origin; done
Already up-to-date.
Already up-to-date.
Already up-to-date.
Already up-to-date.

$ ./create_build.sh -c ../../../../Build/apf_build.conf -b release-2.0 -v 2.0.1 -m configpack
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF release                                                      ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 13:16:26
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

[INFO] Loading configuration file ../../../../Build/apf_build.conf
[INFO] Set global parameters ...
[INFO] Current build version: 2.0.1-2014-05-02-1316
[INFO] Exporting code branch release-2.0 to workspace ...
[INFO] create configpack release
[INFO] clean up temporary directories

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 13:16:29
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

Sofern Sie ein lokales Repository nutzen, kann es notwendig sein, die
Branches zunächst lokal verfügbar zu machen. Dies können Sie mit einem

``` bash
for branch in $(git branch --list -a | grep -E "release|feature" | tr -d " " | cut -d "/" -f 3); do git branch -t $branch origin/$branch; done
```

für jedes der konfigurierten Repositores erledigen.

Ein vollständiges Release lässt sich wie folgt erstellen:

``` bash
Christian@chrislap /cygdrive/c/Users/Christian/Entwicklung/GIT/tools/build/release
$ ./create_build.sh -c ../../../../Build/apf_build.conf -b release-2.0 -v 2.0.1 -m all
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF release                                                      ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 13:39:14
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

[INFO] Loading configuration file ../../../../Build/apf_build.conf
[INFO] Set global parameters ...
[INFO] Current build version: 2.0.1-2014-05-02-1339
[INFO] Exporting code branch release-2.0 to workspace ...
[INFO] generate documentation
[INFO] copy html documentation to current release folder
[INFO] create zip documentation files
[INFO] build code bases for php5 release
[INFO] create codepack release for php5
[INFO] create configpack release
[INFO] generate demopack basis for php5
[INFO] create demopack release packages for php5
[INFO] generate vbc example basis for php5
[INFO] create vbc example release packages for php5
[INFO] generate calc example basis for php5
[INFO] create calc example release packages for php5
[INFO] generate modules example basis for php5
[INFO] create modules example release packages for php5
[INFO] clean up temporary directories

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 13:46:41
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

<languages />