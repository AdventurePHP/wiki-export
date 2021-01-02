Wie vor einiger Zeit bereits hier
<http://forum.adventure-php-framework.org/de/viewtopic.php?f=10&t=771>
angefangen gibts nun das einstrechende Proposal

## Idee

Um Komponenten einfach miteinander verknüpfen zu können, ist das Ziel,
dass jede Komponente an einer zentralen Stelle melden kann was sie
gerade gemacht hat. Wenn in dieser zentralen stelle eine Reaktion auf
dieses Ereignis definiert ist, wird darauf reagiert. Die meldende
Komponente muss dies aber nicht wissen.

### Wann wird reagiert

Es soll sowohl die Möglichkeit geben auf ein Event gleich zu reagieren
als auch zu einem verspäteten Zeitpunkt

## Begriffsdefinitionen

Damit keine Unklarheiten entstehen:

### Event

Jenes Objekt das mit den entsprechenden Infos gefüllt wird und dann an
den Event-Handler übergeben wird. Der Parameter $Info darf nur entweder
nur von Typ String oder ein mit Strings gefülltes Array sein, damit
gewährleistet werden kann, dass dieses auch in der DB gespeichert werden
kann.

Falls $Store aktiviert wird, wird Event automatisch gespeichert.

Sobald der Parameter ExecutionTime gesetzt wird, wird das Event erst zu
dem entsprechenden Zeitpunkt abgearbeitet und vorher so lange in der DB
gespeichert.

### EventHandler

Hier wird definiert, wie auf das Event reagiert werden soll

### EventDispatcher

Ist die zentrale Anlaufstation. Es wird das Event an den EventDispatcher
übergeben. Dieser läd dann alle EventHandler und übermittelt diesen das
Event. Die EventHandler reagieren anschließend darauf.

## Funktionen

## Klassen, Interfaces und Config

### Event

``` php
class Event {

    protected $Name;
    protected $Info;
    protected $ExecutionTime;
    protected $Store;
    protected $Priority;
    protected $Context;
    protected $Language = 'de';
    protected $Cancelled = false;

    public function __construct($Name, $Context, $Language = 'de', $Info = null) {
        $this->Name = $Name;
        $this->Info = $Info;
        $this->Context = $Context;
        $this->Language = $Language;
    }

    public function getContext() {
        return $this->Context;
    }

    public function setContext($Context) {
        $this->Context = $Context;
        return $this;
    }

    public function getLanguage() {
        return $this->Language;
    }

    public function setLanguage($Language) {
        $this->Language = $Language;
        return $this;
    }

    public function getName() {
        return $this->Name;
    }

    public function setName($Name) {
        $this->Name = $Name;
        return $this;
    }

    public function isCancelled() {
        return $this->Cancelled;
    }

    public function Cancel() {
        $this->Cancelled = true;
        return $this;
    }

    public function getInfo() {
        return $this->Info;
    }

    public function getExecutionTime() {
        return $this->ExecutionTime;
    }

    public function setExecutionTime($ExecutionTime) {
        $this->ExecutionTime = $ExecutionTime;
    }

    public function getStore() {
        return $this->Store;
    }

    public function setStore($Store) {
        $this->Store = $Store;
    }
    public function getPriority() {
        return $this->Priority;
    }

    public function setPriority($Priority) {
        $this->Priority = $Priority;
    }

}
```

### EventHandler

``` php
interface EventHandler {
    public function handle(Event &$Event);
}
```

### EventDispatcher

``` php
use APF\core\eventhandler\Event;

class EventDispatcher extends APFObject {

    protected $Handlers = array();
    protected $Events = array();

    public function init($initParam) {

        if (isset($initParam['namespace'])) {
            $ConfigNamespace = $initParam['namespace'];
        } else {
            $ConfigNamespace = 'APF\core\eventhandler';
        }
        if (isset($initParam['filename'])) {
            $ConfigFilename = $initParam['filename'];
        } else {
            $ConfigFilename = 'autoload.ini';
        }
        $Config = $this->getConfiguration($ConfigNamespace, $ConfigFilename);

        foreach ($Config->getSectionNames()as $EventName) {
            $EventSection = $Config->getSection($EventName);

            $Sectionbuffer=$EventSection->getSection('conf');
            foreach ($Sectionbuffer->getSectionNames() as $secName) {
               $sec=$Sectionbuffer->getSection($secName);

                $namespace = $sec->getValue('namespace');
                $class = $sec->getValue('class');

                try {
                    $CatchPreviousEvents = $sec->getValue('PreviousEvents');
                    if ($CatchPreviousEvents == 'true') {
                        $CatchPreviousEvents = true;
                    } else {
                        $CatchPreviousEvents = false;
                    }
                } catch (Execption $e) {
                    $CatchPreviousEvents = true;
                }

                try {
                    $initParameters = $sec->getValue('init');
                } catch (Execption $e) {
                    $initParameters = null;
                }

                $Handler = $this->getAndInitServiceObject($namespace, $class, $initParameters);
                $this->addHandler($EventName, $Handler, $CatchPreviousEvents);
            }
        }

    }

    public function addHandler($EventName, EventHandler $Handler, $CatchPreviousEvents = true) {
        if (!isset($this->Handlers[$EventName])) {
            $this->Handlers[$EventName] = array();
        }
        $this->Handlers[$EventName][] = $Handler;

        // handle events which were fired before attaching this eventhandler
        if ($CatchPreviousEvents) {
            if (isset($this->Events[$EventName])) {

                foreach ($this->Events[$EventName] as $Event) {
                    $Handler->handle($Event);
                }
            }
        }

        return $this;
    }

    public function &trigger($EventName, $Info = null, $Context = null, $Language = null) {
        if ($Context === null) {
            $Context = $this->getContext();
        }
        if ($Language === null) {
            $Language = $this->getLanguage();
        }
        $Event = new Event($EventName, $Context, $Language, $Info);

        return $this->triggerEvent($Event);
    }

    public function &triggerEvent(Event &$Event) {
        $Name = $Event->getName();

        // send event to all relevant eventhandlers
        if (isset($this->Handlers[$Name])) {
            foreach ($this->Handlers[$Name] as $Handler) {
                $Handler->handle($Event);
                if ($Event->isCancelled() === true) {
                    break;
                }
            }
        }

        // save event for handlers which get attached later
        if (!$Event->isCancelled()) {
            if (!isset($this->Events[$Name])) {
                $this->Events[$Name] = array();
            }
            $this->Events[$Name][] = $Event;
        }

        return $Event;
    }

}
```

### Config

``` ini
[Eventname]
conf.1.class="APF\modules\blog\eventhandler\DeleteBlogCategory"
conf.1.init="default"

conf.2.class="APF\core\eventhandler\events\CancelEvent"
```

# TODO's, weitere Ideen und mögliche Probleme

-   Herausfinden in wie weit das alles bereits in den CronEvents
    <http://forum.adventure-php-framework.org/de/viewtopic.php?f=11&t=1132>
    umgesetzt wurde.
-   EventHandler entwerfen der Event in die DB schiebt und so für die
    spätere Abarbeitung speichert
-   Zeitstempelfunktion im Dispatcher entwerfen.
-   Funktion anbieten, mit der noch nicht abgearbeitete Events aus der
    DB geholt werden (entweder alle oder nur bestimmte (executiontime,
    name oder teile des namens oder zb mittels prioritätskriterium).
-   Prüfen ob ein Handler bereits auf ein Event reagiert hat (event
    log?)
-   Priorität eines Events definieren
-   was soll mit doppelten Events passieren (Eine Möglichkeit ist die
    Ersetzung, eine andere, eine nacheinander gestaltete Bearbeitung.
    Hier wäre vermutlich die Ersetzung intelligenter, denn wirklich
    (Inhalts-)gleiche Events (z.B. zweimal abgeschickte Bestellung)
    sollten nur einmal verarbeitet werden.)
-   in der Konfiguration hinterlegen WIE das Objekt erzeugt werden soll
    (Direkt, DIService, getAndInitServiceObject)