## Einleitung

Das Newssystem ist eine Implementierung eines typischen
Neuigkeitensystems, wie aus auf vielen Webseiten im Einsatz ist. Über
ein Backend kann man News anlegen, editiern und löschen, welche in einem
Frontend (häufig auf der Startseite) angezeigt werden. Die News werden
in der Datenbank gespeichert. Bei der Anzeige im Frontend wird, wenn
benötigt, automatisch in Seiten unterteilt, der URL-Parameter hierfür,
sowie die Anzahl der News pro Seite lassen sich in einer
Konfigurationsdatei ebenso festlegen, wie das Aktivieren oder
Deaktivieren von HTML in den Neuigkeiten.

Eine Beispielintegration inklusive Installation-Wizzard ist in der
Sandbox zu finden.

## Feature Übersicht

-   Backend (anlegen, bearbeiten, löschen)
-   Frontend mit Pagination (Einteilung der News in mehrere Seiten,
    Navigation um Seiten zu wechseln inklusive)
-   Beeinflussung der Pagination in der Konfiguration (Parametername und
    Anzahl News pro Seite)
-   Mehrsprachig (de & en bereits enthalten)
-   Titel, Autor (optional), Newstext
-   Erlauben/Verbieten von HTML im Newstext per Konfiguration(Standard:
    DEAKTIVIERT)
-   Einfache Anpassung des Aussehens über CSS möglich
-   Mehrfachverwendung unabhängiger Newsbereiche auf der selben Webseite
    und der selben Datenbank über Application Key

## Installation & Konfiguration

Die Beispielkonfigurationen unter */config/extensions/news/* müssen in
einen Ordner mit dem Name des Contextes der Applikation kopiert werden
(außer in der Bootstrap Datei wurde die Context-Unterscheidung der
Konfiguration deaktiviert) und der Environment-Prefix (EXAMPLE_) muss
auf den passenden geändert werden (meist: DEFAULT_).

In der Datei *\*_news.ini* kann die Pagination beeinflusst werden,
sowie die Aktivierung von HTML in den Neuigkeiten konfiguriert werden:

``` ini
[Paging]
EntriesPerPage = "6"
PageParameter = "newspage"

[General]
AllowHtml = "FALSE"
```

Die *\*_serviceobjects.ini* muss in jedem Fall angepasst werden:

``` ini
[NewsManager]
servicetype = "SINGLETON"
class = "APF\extensions\news\biz\NewsManager"
init.gorm.method = "setORM"
; If you already have a configured DI-GORM, just edit this default values and set your existing configuration as target instead
init.gorm.namespace = "extensions::news"
init.gorm.name =  "GORM"


[GORM]
servicetype = "SINGLETON"
class = "APF\modules\genericormapper\data\GenericORRelationMapper"
conf.ext.method = "setConfigFileExtension"
conf.ext.value = "ini"
conf.namespace.method = "setConfigNamespace"
conf.namespace.value = "extensions\news"
conf.affix.method = "setConfigNameAffix"
conf.affix.value = "news"
conf.conn.method = "setConnectionName"
conf.conn.value = ""      ; Set the name of the configured database connection here
conf.debug.method = "setLogStatements"
conf.debug.value = "false"
```

Sofern bereits ein GORM per DI-Serviceobject konfiguriert ist, kann in
der "NewsManager"-Sektion diese Konfiguration angegeben werden, einfach
den **init.gorm.namespace** auf die vorhandene Konfiguration setzen, und
**init.gorm.name** auf den Namen der Sektion in dieser Konfiguration.
Dabei dürfen sie nicht vergessen, dass die GORM-Konfigurationen der
News-Extension noch ihrem bereits konfigurierten GORM angefügt werden
müssen.

Falls dies noch nicht der Fall ist, wird der obere Teil belassen wie er
ist, und der GORM mit dem unteren Bereich konfiguriert. Hier muss
lediglich noch der Name der Datenbankverbindung, wie er in
*/core/database/{Context}/{ENVIRONMENT}_connections.ini* definiert
wurde angegeben werden in **conf.conn.value**.

Anschließend muss noch die Datenbanktabelle installiert werden. Hierfür
kann entweder die Datei /extensions/news/data/setup.sql manuell in der
Datenbank ausgeführt werden, oder das Setup-/Updatetool des
GenericORRelationMapper verwendet werden (siehe GORM-Doku für weitere
Informationen hierzu).

## Verwendung

Die Extension funktioniert generell out-of-the-box, wenn also keine
weiteren Anpassungen notwendig sind. Allerdings muss zuerst noch eine
css Datei für das Aussehen eingebunden werden. Das Aussehen kann ganz
leicht durch Verwendung einer alternativen CSS-Datei angepasst werden,
hierfür sind meist keine Änderungen an den Templates notwendig. Sofern
erst einmal das Standardaussehen verwendet werden soll, muss die CSS
Datei von *extensions/news/pres/css/news.css* auf der Seite eingebunden
werden (entweder durch kopieren in den öffentlichen Ordner, oder
Auslieferung mit der JsCssInclusion-Extension von mir).

Front- und Backend können jeweils einfach durch einsetzen der
*<core:importdesign />*-Taglib eingebunden werden. Das Backend bringt
ein eigenes kleines Menü mit, welches dann auch automatisch mit
eingebunden wird. Durch angabe des Attributs **app-ident=""** im
importdesign-Tag kann eine Unterscheidung verschiedene Newsbereich in
ein und der selben Webseite und Datenbank vorgenommen werden. Jede
Newsinstanz ist davon von den anderen unabhängig und es werden nur die
News dort angezeigt, welche diesem Application Key zugeordnet sind. Wird
kein Identifier übergeben wird automatisch der aktuelle Context als Key
verwendet.

### Einbindung des Frontend

``` xml
<core:importdesign namespace="APF\extensions\news\pres\templates" template="frontend" />
```

Soll ein App-Key übergeben werden reicht:

``` xml
<core:importdesign namespace="APF\extensions\news\pres\templates" template="frontend" app-ident="examplekey" />
```

Es werden dann nur die News angezeigt welche im Backend mit dem
entsprechenden Key angelegt wurden.

### Einbindung des Backend

``` xml
<core:importdesign namespace="APF\extensions\news\pres\templates" template="backend"/>
```

Die Angabe eines App-Keys gestaltet sich hier analog zum obigen
Beispiel.

## Verwendungsbeispiel

Ein fertig konfiguriertes Beispiel des Newssystems mit
Installations-Wizzard findet sich in der APF-Sandbox.