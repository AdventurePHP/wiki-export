# Einleitung

Das Tools "MultiFileUpload" stellt eine einfach zu verwendene Komponente
zum Erstellen eines Upload-Formulares für diverse Dateien zur Verfügung.
Die Komponente wurde vom APF-User "welworx" - Werner Liemberger -
entwickelt und basiert auf der jQuery-Erweiterung "jQuery-File-Upload".
Folglich ist die vorherige Implementierung von jQuery in der Version 1.6
oder höher und das jQuery-UI in der Version 1.8 oder höher
Grundvoraussetzung. Alle weiteren Informationen dazu finden Sie unter
dem Kapitel Voraussetzungen.

# Voraussetzungen

Um das Tool MultiFileUpload verwenden zu können, müssen einige wichtige
Voraussetzungen erfüllt sein, ohne die eine fehlerfreie Verwendung nicht
garantiert werden kann:

-   jQuery Version 1.6+
-   jQuery-UI Version 1.8+
-   volle Schreibrechte im temporären Upload-Verzeichnis

Ein Tutorial zeigt Ihnen, wie einfach das Tool MultiFileUpload zu
verwenden ist und wie es in Ihre Anwendung eingebunden werden kann.

# Aufbau des MultiFileUpload

Das MultiFileUpload liefert als Kernkomponente den
"MultiFileUplaodManager". Weiterhin wird eine bereits vorgefertigte
Taglib mitgelieferrt, die auf dem MultiFileUploadManager aufbaut und
welche in Anwendungen schnellen Einsatz finden kann. Anhand des
MultiFileUploadManager kann jedoch auch eine eigene Taglib für Ihre
eigenen Bedürfnisse erstellt werden.

## Die Kernkomponente MultiFileUploadManager

Das Herzstück des MultiFileUpload stellt der "MultiFileUploadManager"
dar. Die Klasse stellt alle Methoden für den erfolgreichen Einsatz des
Uploads mit einer Taglib zur Verfügung. Die gesamte Methoden-Übersicht
finden Sie in der API-Dokumentation des APF.

## Die Taglib form_taglib_multifileupload

Die vorgefertigte Taglib ermöglicht den schnellen und einfachen Einsatz
des Tools "MultiFileUpload". Alles, was Sie tun müssen, ist die
notwendigen JavaScripte wie jQuery, jQery-UI sowie jQery-Fileupload
vorher einzubinden und ein Formular mit den APF-Methoden zu definieren,
welches anschliessend die Taglib "form_taglib_multifileupload"
einbindet. Alle weiteren Informationen finden Sie im Tutorial zum
"MultiFileUpload".

## Ein DocumentController

Um mit dem MutliFileUpload effektiv arbeiten zu können, müssen Sie einen
DocumentController benutzen, der zum Einen Ihr Formular ausgibt und zum
Anderen nach dem Absenden des Formulares die Dateien weiter verarbeitet.
Der MultiFileUploadManager bietet dafür bereits eine geeignete Methode
namens *moveFile*. *moveFile($uploadFileName, $dir, $name)* erwartet als
Übergabeparameter folgendes:

*$uploadFileName* - Name der beriets hochgeladenen Datei, die verschoben
werden soll

*$dir* - Verzeichnis, in welches die Datei verschoben werden soll

*$name* - Dateiname, welchen die Datei nach dem Verschieben erhalten
soll

Eine Implementierung könnte so aussehen:

``` php
class DemoController extends base_controller {
   public function transformContent() {

      $form = &$this->getForm('file_upload');

      $uploadtest = $form->getFormElementByName('testfield');

      if ($form->isSent()) {
         foreach ($files as $file) {
            $uploadtest->moveFile($file['uploadname'], APPS__PATH . '/tools/form/multifileupload/newdir', $file['uploadname']);
         }
      } else {
         $form->transformOnPlace();
      }
   }

}
```

Wie Sie sehen, wird zu Beginn das Formular definiert und das
MultiUploadFile-Feld bekannt gemacht. Nachdem das Formular erfolgreich
abgesendet wurde, wird jede hochgeladene Datei in der forech-Schleife
einzeln bearbeitet. Über die Methode moveFile wird die Datei an einen
anderen Ort verschoben. Sie können dabei den namen der Datei am Zielort
auch ändern. Im Beispiel wird der kryptische Name allerdings
beibehalten. Wichtig ist als Pfadangabe absolute Pfade vom
Wurzelverzeichnis der Applikation. Dafür bietet sich die Konstante
*APPS__PATH* an.

# Verhalten unterschiedlicher Browser

Wie so oft werden nicht alle Features des jQuery-Fileupload von allen
Browsern in gleichem Umfang unterstützt. Eine genaue Liste finden Sie
auf der Dokumentations-Seite des Herstellers:
[<https://github.com/blueimp/jQuery-File-Upload/wiki/Browser-support>](https://github.com/blueimp/jQuery-File-Upload/wiki/Browser-support)

So viel vorneweg: Firefox sowie Google Chrome eignen sich am besten.
Allerdings wurde das Tool bei der Entwicklung hauptsächlich mit dem
Internet Explorer getestet, bei dem alles Tests erfolgreich waren.

# Tutorial

## Einleitung

Dieses Tutorial widmet sich dem Einbau des Tool "MultiFileUpload" in
Ihre Webseite mit den zur Verfügung stehenden Komponenten des APF. Um
die genauen Schritte nachvollziehen zu können, sehen Sie sich ebenfalls
das Template "demo" an, welches Sie im Namespace

``` html4strict
tools::form::multifileupload::pres::templates
```

finden. Das Tutorial behandelt die "demo"-Anwendung und baut darauf auf.

## Vorraussetzungen

Wie in der Dokumentation bereits erwähnt, werden die die Frameworks
"jQuery" sowie "jQuery-UI" benötigt. Diese werden wir mit den vom APF
zur Verfügung gestellten Komponenten einbinden. Zu Beginn wollen wir uns
allerdings eine einheitliche Ordner-Struktur anlegen, um unsere
benötigten Datei darin zu organiseren.

### Ordnerstruktur

Im Auslieferungszustand besteht das Tool "MultiFileUplaod" bereits aus
folgender Struktur:

``` html4strict
tools / form / multifileuplaod
   / actions
       - MultiFileDeleteAction.php
       - MultiFileGetFileAction.php
       - MultiFileUploadAction.php
   / biz
       - MultiFileUploadManager.php
   / pres
       / css
           - jquery.fileupload-ui.css
       / documentcontroller
           - DemoController.php
       / js
           - jquery.fileupload-ui.js
           - jquery.fileupload.js
       / taglib
           - form_taglib_multifileuplaod.php
       / templates
           - demo.html
   / uploaddir
```

### jQuery und jQuery-UI mit passenden Theme herunterladen

Sollten Sie bereits jQuery oder jQery-UI im Einsatz haben, lesen Sie
bitte im Abschnitt "5.3 jQuery einbinden"
([MultiFileUpload\#jQuery_einbinden](/MultiFileUpload#jQuery_einbinden "wikilink"))
weiter. Laden Sie sich das neuste jQuery-Bundle von der offiziellen
jQuery-Webseite herunter:
[<http://jquery.com/download/>](http://jquery.com/download/)

Laden Sie sich das neueste jQuery-UI-Bundle von der offiziellen
jQuery-UI-Webseite herunter:
[<http://jqueryui.com/download/>](http://jqueryui.com/download/)

Zusätzlich benötigen Sie noch ein jQuery-UI-Theme, wodurch die
verwendeten Info-Boxen eine optische Gestaltung erhalten. Den
sogenannten "ThemeRoller" finden Sie unter folgender Adresse:
[<http://jqueryui.com/themeroller/>](http://jqueryui.com/themeroller/)

Dort können Sie nach Ihren Wünschen das Theme erstellen und
herunterladen oder ein bereits vorgefertigtes Theme wählen.

### jQuery und Co. in die Ordnerstruktur einfügen

Bitte beachten Sie: Zum Zeitpunkt der Erstellung des Tutorial war die
jQuery-Version 1.7.2 und die jQuery-UI-Version 1.8.21. Als Theme wird im
weiteren Verlauf beispielhaft "south-street" verwendet.

Im nächsten Schritt fügen wir die herunter geladenen Dateien in die
vorhandene Ordnerstruktur ein. Kopieren Sie dazu die Dateien
"jquery-1.7.2.min.js" und "jquery-ui-1.8.21.custom.min.js" in den Ordner

``` html4strict
tools / form / multifileuplaod
   / pres
       / js
```

sodass es am Ende so aussehen sollte:

``` html4strict
tools / form / multifileuplaod
   / actions
       - MultiFileDeleteAction.php
       - MultiFileGetFileAction.php
       - MultiFileUploadAction.php
   / biz
       - MultiFileUploadManager.php
   / pres
       / css
           - jquery.fileupload-ui.css
       / documentcontroller
           - DemoController.php
       / js
           - jquery-1.7.2.min.js
           - jquery-ui-1.8.21.custom.min.js
           - jquery.fileupload-ui.js
           - jquery.fileupload.js
       / taglib
           - form_taglib_multifileuplaod.php
       / templates
           - demo.html
   / uploaddir
```

Das von Ihnen gewählte Theme kopieren Sie bitte in den Ordner

``` html4strict
tools / form / multifileuplaod
   / pres
       / css
```

sodass es am Ende so aussehen sollte:

``` html4strict
tools / form / multifileuplaod
   / actions
       - DeleteAction.php
       - GetFileAction.php
       - MultiFileUploadAction.php
   / biz
       - MultiFileUploadManager.php
   / pres
       / css
           / south-street
               jquery-ui-1.8.21.custom.css
           - jquery.fileupload-ui.css
       / documentcontroller
           - DemoController.php
       / js
           - jquery-1.7.2.min.js
           - jquery-ui-1.8.21.custom.min.js
           - jquery.fileupload-ui.js
           - jquery.fileupload.js
       / taglib
           - form_taglib_multifileuplaod.php
       / templates
           - demo.html
   / uploaddir
```

Wichtig dabei ist, dass Theme in einem Unterordner im Ordner css zu
verwalten, da es sonst zu Problemen mit dem Theme kommt. Die
mitgelieferten Bilder des Themes müssen Sie ausserhalb des APF-Ordner in
einem Unterordner namens "images" aufbewahren.

**1. Beispiel:** Ihre Webseite ist über die folgende Domain erreichbar:
"<http://www.topdomain.tld>". Die Bild-Dateien des Theme müssen über
folgende Verzeichnis auf Ihrem Server erreichbar sein:
"<http://www.topdomain.tld/images>".

**2. Beispiel:** Ihre Webseite ist über die folgende Domain erreichbar:
"<http://www.topdomain.tld/unterordner>". Die Bild-Dateien des Theme
müssen über folgende Verzeichnis auf Ihrem Server erreichbar sein: "
<http://www.topdomain.tld/unterordner> /images".

## jQuery einbinden

Nachdem wir alle notwendigen Dateien an den richtigen Stellen platziert
haben, können wir diese einbinden. Schauen Sie sich zum besseren
Verständnis auch das Template "demo" an. Wir verwenden zum Einbinden des
.js sowie .css-Dateien die APF-Erweiterung "Html-Header". Im Wiki finden
Sie einen ausführlichen Beitrag zu Verwendung der Erweiterung:
[Html-Header_Erweiterung](/Html-Header_Erweiterung "wikilink")

### Taglibs definieren

In den Head-Bereich Ihres Dokumentes fügen wir zuerst die beiden
Html-Header Taglibs "addjs" sowie "addcss" ein. Anhand der Taglibs
können wir .js sowie .css-Dateien einbinden.

``` html4strict
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="addcss" />
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="addjs" />
```

Bitte beachten Sie: Die Html-Header-Erweiterung ermöglicht auch das
Einfügen von .js-Dateien am Ende des Dokumentes. Für weitere Details
dazu lesen Sie bitte den Wiki-Beitrag.

### .js-Dateien einfügen

Nachdem die Taglibs vorhanden sind, können wir alle notwendigen
.js-Dateien einfügen. Zuerst jQuery sowie jQuery-UI:

``` html4strict
<htmlheader:addjs namespace="tools::form::multifileupload::pres::js" filename="jquery-1.7.8.min" priority="100" />
<htmlheader:addjs namespace="tools::form::multifileupload::pres::js" filename="jquery-ui-1.8.22.custom.min"  priority="99" />
```

Über das Attribut "priority" können wir die Reihenfolge der
einzufügenden Dateien beeinflussen. Je höher die "priority", desto
früher wird die Datei eingebunden.

Weiter mit der jQuery-Erweiterung "FileUpload":

``` html4strict
<htmlheader:addjs namespace="tools::form::multifileupload::pres::js" filename="jquery.fileupload" priority="90" />
<htmlheader:addjs namespace="tools::form::multifileupload::pres::js" filename="jquery.fileupload-ui" priority="89" />
```

Auch hier setzen wir die "priority" auf einen niedrigeren Wert als
jQuery und jQuery-UI. Denn was würde es nützen, die jQuery-Erweiterung
vor dem eigentlichen jQuery bekannt zu machen?

### .css-Dateien einfügen

Nachfolgend fügen wir noch alle benötigten StyleSheets ein:

``` html4strict
<htmlheader:addcss namespace="tools::form::multifileupload::pres::css" filename="jquery.fileupload-ui" />
<htmlheader:addcss namespace="tools::form::multifileupload::pres::css::south-street" filename="jquery-ui-1.8.22.custom" />
```

### Head-Knoten einfügen

Nachdem wir alle benötigten Dateien eingebunden haben, sind wir bereit,
den Head von der Erweiterung Html-Header ausgeben zu lassen. Laut
Dokumentation muss dazu ein weitere Tag definiert werden, "welcher sich
mithilfe eines Output-Filters um die Ausgabe der nachträglich
hinzugefügten Informationen kümmert:" Im Head-Bereich unseres Dokumentes
fügen wir noch zwei weitere Zeilen Code ein:

``` html4strict
<core:addtaglib namespace="extensions::htmlheader::pres::taglib" prefix="htmlheader" class="gethead" />
<htmlheader:gethead />
```

Damit sind alle notwendigen .js und .css-Dateien bekannt und warten auf
ihren Einsatz.

<div style="border: 1px solid red; background: #F9F9F9; padding: 5px; font-weight: bold;">

Beachten Sie bitte: Denken Sie an die notwendige Konfiguration des
HtmlHeaders. Dieser benötigt ebenfalls eine Konfiguration, die durch den
MultiFileUpload nicht mitgeliefert wird. Weitere Informationen dazu
finden Sie in der Dokumentation des HtmlHeader [Html-Header
Erweiterung](/Html-Header_Erweiterung "wikilink")

</div>

## Das File-Upload Formular

Als nächstes erstellen wir uns ein Formular, welches zum Hochladen der
Dateien dienen soll. Das Tool "MultiFileUpload" erstellt dabei für
Web-Grafiken automatisch eine Vorschau und zeigt den Upload-Fortschritt
an.

Bitte beachten Sie: Wie in der Dokumentation zum MultiFileUpload schon
erwähnt, kommt es bei den vielen unterschieldichen Browsern teilweise zu
eingeschränkter Funktionalität. Am besten bewährten sich der Firefox
sowie Google Chrome. Allerdings ist die grundsätzliche Funktionalität
(Upload, Löschen, Vorschau) mit allen Browsern möglich.

Für jedes mit dem APF erzeugte Formular wird eine Taglib benötigt. Diese
binden wir zu Beginn ein:

``` html4strict
<core:addtaglib namespace="tools::form::taglib" prefix="html" class="form" />
```

Anschliessend können wir unser Formular erstellen:

``` html4strict
<html:form name="file_upload" >
      <form:addtaglib namespace="tools::form::multifileupload::pres::taglib" prefix="form" class="multifileupload" />
      <form:multifileupload name="testfield" />

      <form:button name="send" value="Send" />
</html:form>
```

Das Tool MultiFileUpload bietet ebenfalls eine eigene Taglib, welche
eingebunden werden muss und welche sich um die Erstellung sämtlicher
JavaScripte kümmert, die zum erfolgreichen Betrieb des Tools notwendig
sind. Am Ende fügen wir noch einen "Send"-Button ein, der vom User
angeklickt werden muss, sobald alle Uploads beendet sind.

Damit ist das Template fertig und es steht zum Einsatz auf dem Server
bereit. Allerdings ist der FileUpload noch nicht funktionsfähig, da
wichtige Einstellungen fehlen, welche im letzten Schritt des Tutorials
behandelt werden sollen.

Bitte beachten Sie: Für die Ausgabe des Formulares benötigt es natürlich
einen DocumentController. Dieser ist in der "demo" bereits implementiert
und gibt nach dem Absenden des Formulares mit dem "Send"-Button
lediglich die Dateiinformationen der hochgeladenen Dateien zurück. Den
DemoControllers finden Sie im Verzeichnis "documentcontroller". Der
Vollständigkeit halber ist der Inhalt des Controllers nachfolgend
aufgeführt:

``` php
class DemoController extends base_controller {

    public function transformContent() {

        $form = &$this->getForm('file_upload');

        $uploadtest = $form->getFormElementByName('testfield');
        $uploadtest->uploadFiles();


        if ($form->isSent()) {
            $files=$uploadtest->getFiles();

            // Für die weitere Verarbeitung steht nun das Array files mit allen Dateien zur Verfügung.
            echo printObject($files);

        } else {
            $form->transformOnPlace();
        }
    }

}
```

In der Dokumentation zum MultiFileUpload finden Sie auch einen
Beispiel-Controller zum Verschieben bereits hochgeladener Dateien.

## Die Konfiguration (Config)

Das Tool "MultiFileUpload" benötigt dringend zwei Konfigurationsdateien,
um online lauffähig zu sein. Diese "configs" legen Sie bitte im
Namespace

``` php
config::tools::form::multifileupload::<Ihr Context>
```

an. Bei weiteren Fragen zum Thema Konfiguration lesen Sie bitte die
ausführliche Dokumentation:
[<http://adventure-php-framework.org/Seite/134-Konfiguration>](http://adventure-php-framework.org/Seite/134-Konfiguration)

### Die Sprache - \*_language.ini

Auch das Tool "MultiFileUpload" ist multilanguage-fähig. Dazu steht die
Konfigurationsdatei "<Ihr Environment>_language.ini" zur Verfügung. Das
Tool wird mit zwei unterstützten Sprachen "de" sowie "en" ausgeliefert.
Weitere Sprachen fügen Sie einfach an das Ende der Datei.

Die Konfiguration für die deutsche Sprache ist folgende:

``` ini
[de]
botton.value = "Upload"
delete.label = "Löschen"
delete.message = "Soll die Datei wirklich vom Server entfernt werden?"
delete.ok = "Löschen"
delete.no = "Nicht löschen"
delete.title = "Wirklich löschen?"
cancel.label = "Abbruch"

upload.button.label = "Upload"
upload.label = "Hier Klicken, oder Datei hierher ziehen."

filesize.title = "Überschrittene Dateigröße"
filesize.message = "Diese Datei ist zu gross! Limit:"
filesize.ok = "Ok"

filetype.title = "Unerlaubter Dateityp"
filetype.message = "Dieser Dateityp ist nicht erlaubt. Erlaubt sind"
filetype.ok = "Ok"
```

Um bestimmte Ausdrücke nach Ihren Bedürfnissen zu ändern, passen Sie den
Text einfach an und speichern Sie die Konfiguration auf dem Server.

### Die Action-Konfiguration - \*_actionconfig.ini

Um die Dateien auf den Server zu kopieren, zu löschen oder eine Vorschau
anzuzeigen, bietet das Tool "MultiFileUpload" drei "actions" an. Diese
befinden sich im Namespace

``` html4strict
tools::form::multifileupload::actions
```

und benötigen ebenfalls eine Konfiguration. Legen Sie dazu eine
Konfigurationsdatei "<Ihr Environment>_actionconfig.ini" im
Konfigurations-Namespace an. Der Inhalt der Datei sollte so aussehen:

``` css
[multifileupload]
FC.ActionNamespace = "tools::form::multifileupload::actions"
FC.ActionClass = "MultiFileUploadAction"

[multifiledelete]
FC.ActionNamespace = "tools::form::multifileupload::actions"
FC.ActionClass = "MultiFileDeleteAction"

[multifilegetfile]
FC.ActionNamespace = "tools::form::multifileupload::actions"
FC.ActionClass = "MultiFileGetFileAction"
```

### Die MultiFileUpload-Konfiguration - \*_multifileuplaod.ini

In weiteren Versionen des MultiFileUpload sind noch ausführlichere
Konfigurationsmöglichkeiten geplant. Bisher lässt sich über diese
Konfigurationsdatei nur der Pfad zum Ladebild einstellen. Das Ladebild
wird beim Upload dargestellt und muss direkt erreichbar sein. Es bietet
sich an, das Bild ebenfalls wie die Bilder des jQuery-UI in demselben
Verzeichnis aufzubewahren.

Um mehrere Upload-Formulare auf den Webseiten anbieten zu können und
dabei unterschiedliche Konfigurationen anlegen zu können, werden alle
Konfigurationspartner in Sektionen aufgeteilt. Die Sektion muss
denselben Namen haben, wie das <form:multifileupload>-Feld.

**Beispiel:** Ihr Formluar enthält das nachfolgende
MultiFileUpload-Feld:

``` html4strict
<form:multifileupload name="uploadPremiumUser" />
```

Ihre Konfiguration sollte danach folgend aussehen:

``` css
[uploadPremiumUser]
loadingimage.dir = "../images/multiuplaod" ; Verzeichnis absolut vom Wurzelverzeichnis aus
loadingimage.name = "loading.gif" ; Dateiname des Bildes
```

Die Parameter *loadingimage.dir* und *loadingimage.name* sollten
selbsterklärend sein. Geben Sie hier den Pfad zum Ladebild sowie den
Ladebild-Namen ein.

Die MultiFileUpload-Konfiguration ist damit fertig und einsatzbereit.

### Erlaubte Dateitypen sowie Dateigrösse

Sie können die erlaubten Dateitypen sowie die erlaubte Dateigrösse
anhand der Attribute "allowed-mime-types" und "max-file-size" anpassen.
Per Default eingestell sind folgende Dateitypen:

``` html4strict
application/pdf, application/x-download (manche PDF´s besitzen diesen MimeType), image/gif, image/jpeg, image/jpg, image/png
```

und folgende Dateigrösse

``` html4strict
10485760 Bytes
```

was 10 MB entspricht. Um diese Einstellungen für Ihre Anwendung zu
ändern, fügen Sie einfach die Attribute "allowed-mime-types" und/oder
"max-file-size" in Ihren <form:multifileupload/>-Tag ein.

**Beispiel für maximal 500 KB:**

``` html4strict
<form:multifileupload name="testfield" max-file-size="512000" />
```

**Beispiel für ausschliesslich PDFs:**

``` html4strict
<form:multifileupload name="testfield" allowed-mime-types="application/pdf,application/x-download" />
```

Es ist auch eine Kombination beider Attribute möglich.

### Das temporäre Upload-Verzeichnis

Die hochgeladenen Datein werden in einem temporären Verzeichnis
gespeichert. Dieses temporäre Upload-Verzeichnis benötigt Schreibrechte,
welche Sie per Hand setzen sollten. Das Upload-Verzeichnis finden Sie
unter folgendem Namespace:

``` html4strict
tools::form::multifileupload::uploaddir
```

und kann derzeit noch nicht manuell geändert werden. Setzen Sie auf
dieses Verzeichnis "Schreib-Rechte" (beispielesweise 0770).

Nachdem Sie über das Formular alle Dateien auf den Server geladen haben
und das Formular über den "Send"-Button abgesendet haben, müssen Sie
sich selbstständig um das Verschieben der Dateien aus dem temporären
Upload-Verzeichnis kümmern. Der "MultiFileUploadManager" bietet dazu
bereits die Methode *moveFile*. Diese könnte aus dem Controller heraus
aufgerufen werden, um die Datei in Ihr Arbeits-Verzeichnis zu
verschieben. Konsultieren Sie dazu bitte auch den Beispiel-Controller
der Dokumentation zum MultiFileUpload.

Für weitere Hinweise bezüglich der Methoden des MultiFileUploadManager
konsultieren Sie bitte die Dokumenatation zum MultiFileUpload.