---
comments: true
layout: post
title: 'Windows Server 2012: Erstellen eines Master Images mit unattend.xml und Sysprep'
---

Nutzt man für die Bereitstellung von Servern und Clients im Unternehmen ein Imaging-Tool wie z.B. Norton Ghost oder WDS, macht es Sinn für verschiedene Einsatzzwecke Images zu erstellen, die bereits bestimmte Software, Treiber und Konfigurationen enthalten. Diese sog. Master Images sollten mit einer Antwortdatei (unattend.xml) und Sysprep vorbereitet werden, so dass sie beim ersten Booten nach der Verteilung, unter anderem, automatisch eine neue SID und einen neuen Computernamen erzeugen. Nur mit Sysprep, ohne unattend.xml funktioniert das ganze zwar auch, allerdings ist dann beim Bootvorgang eines jeden Images manuelles Eingreifen und Konfigurieren erforderlich. Mittels unattend.xml kann dieser Vorgang so weit angepasst werden, dass er vollkommen automatisch abläuft. Zusätzlich können noch sehr viele Konfigurationen angepasst werden, die durch den Sysprep-Vorgang verloren gehen.


## unattend.xml erstellen


Um eine unattend.xml zu erstellen benötigt man den Windows System Image Manager (SIM) der im [Windows ADK](http://www.microsoft.com/de-de/download/details.aspx?id=30652) enthalten ist (und entsprechend früher Teil von WAIK war), sowie das _install.wim_ des bereitzustellenden Betriebssystems. Um SIM zu installieren, reicht es, die Bereitstellungstools aus dem ADK zu installieren. Im ersten Schritt muss dann das install.wim hinzugefügt werden, sowie eine Katalogdatei erzeugt werden (das passiert automatisch nach dem Hinzufügen der install.wim und dauert einige Minuten). Anschließend können die benötigten Komponenten aus dem Windows-Image der Antwortdatei hinzugefügt und konfiguriert werden.

Die Anzahl der verfügbaren Komponenten und die Einstellungsmöglichkeiten sind sehr umfangreich, davon sollte man sich jedoch nicht abschrecken lassen, denn für einfach Bereistellungszwecke werden nur eine Handvoll Komponenten benötigt. Ich werde entsprechend auch nur die Komponenten vorstellen, die benötigt werden, um folgendes Ergebnis zu erlangen:


  * Stand-alone Windows Server 2012 Installation (kein Domainjoin)
  * Beibehalten aller installierten Treiber
  * Automatisches Rearm
  * Deaktivierte Sicherheitseinstellungen des IE für Admins und User
  * Kein automatischer Start des Server-Managers
  * Deutsche Lokalisierung und Zeitzone
  * Einstellung der Monitorauflösung
  * Vergabe des Administrator Passworts
  * Kein Ablaufen des Administrator Passworts
  * Keine automatischen Updates


Diese Konfiguration eignet sich z.B. für eine Lab-Maschine in einer Demo- oder Schulungsumgebung, z.B. für MOC-Schulungen oder andere Labs. Dem entsprechend wird Windows Server auch nicht aktiviert, sondern läuft nur Test-Modus. Nach den genannten Anpassungen sieht die unattend.xml in SIM wie folgt aus:

![Unattend.xml in Windows SIM]({{ site.url }}/images/2013/sim.png )


### 3 generalize


Unter dem Punkt _generalize_ befinden sich Einstellungen die für während der Versiegelung (Generalisierung) des Sysprep-Vorganges angewandt werden. Dieser Vorgang findet direkt nach der Ausführung von Sysprep statt (also noch vor der Image-Erstellung) und entfernt computerspezifische Einstellungen. Wichtige Einstellung an dieser Stellen ist, den Wert `PersistAllDeviceInstalls` unter `Microsoft-Windows-PnpSysprep` auf `true` zu setzen, damit manuell Installierte Treiber nicht entfernt werden. Ebenfalls sehr wichtig ist der Punkt `SkipRearm` unter `Microsoft-Windows-Security-SPP`. Ist dieser auf 1 gesetzt, wird kein Rearm durchgeführt. Dies ist vor allem in der Testphase sehr wichtig, da ein Rearm nur 3 Mal möglich ist. Folglich sollte `SkipRearm` erst im letzten Schritt, direkt vor der Image-Erstellung auf 0 gesetzt werden.


### 4 specialize


Der Punkt _specialize_ beinhaltet Einstellungen, die nach dem Bootvorgang für das Image gelten sollen. Hier werden die Einstellungen für den ServerManager (`DoNotOpenServerManagerAtLogon` unter `Microsoft-Windows-ServerManager-SvrMgrNc` auf `true`) und den IE (`IEHardenUser` und `IEHardenAdmin` unter `Microsoft-Windows-IE-ESC` jeweils auf `false`) vorgenommen.


### 7 oobe System


Der Punkt _oobe_ (out-of-box-experience) ist der umfangreichste in dieser Konfiguration. Hier werden die Einstellungen für Bootvorgang, also für den Setupvorgang gemacht. So wird hier festgelegt, dass es sich um eine unattend-Installation handeln soll, also eine Installation die kein Eingreifen mehr erfordert. Die meisten Einstellungen, die hier gemacht werden, sind selbsterklärend und können weiter untern in der unattend.xml nachgelesen werden. Ich werde hier nur auf einige Einstellungen genauer eingehen. So lassen sich über `Microsoft-Windows-International-Core` die Eingabe- und Systemsprachen definieren. Direkt über den Punkt `Microsoft-Windows-Shell-Setup` lässt sich die Zeitzone auf `W. Europe Standard Time` festlegen. Unterpunkte von `Microsoft-Windows-Shell-Setup` sind `Display` für die Monitoreinstellungen, `UserAccounts` -> `AdministratorPassword` für ebendieses, und `OOBE`. Unter `OOBE` werden die diversen `Hide*` Einstellungen auf `true` gesetzt, dies verhindert das Erscheinen von Eingabemasken während des Bootvorgangs, also das bereits erwähnte unattend-Verhalten. Weitere Interessante Einstellung unter diesem Punkt ist `ProtectYourPC`. Damit werden die Sicherheitseinstellungen festgelegt, 1 entspricht den empfohlenen Sicherheitseinstellungen, 2 bedeutet "Nur Updates werden installiert" und 3 deaktiviert die automatische Installation von Updates.

Um zu verhindern dass das Administrator Passwort abläuft muss unter `Microsoft-Windows-Shell-Setup` der Punkt `FirstLogonCommands` ergänzt werden. Unter `SynchronousCommand` wird als `CommandLine` der Befehl

{% highlight text %}
cmd /C wmic useraccount where "name='Administrator'" set PasswordExpires=FALSE
{% endhighlight %}

übergeben und `RequiresUserInput` auf `false` gesetzt. So wird beim ersten Anmelden automatisch der genannte Befehl ausgeführt, der das Ablaufen des Administrator-Passworts deaktiviert.

Im Anschluss kann die unattend.xml gespeichert werden. Das Ergebnis als XML-Datei sieht wie folgt aus:

{% gist b83786d2ddbdedf59f1b %}


## Sysprep ausführen


Zum Ausführen von Sysprep bietet es sich an, eine kleine Batch-Datei anzulegen, die Sysprep mit den benötigten Parametern und der unattend.xml ausführt.

{% highlight text %}
sysprep.exe /generalize /oobe /reboot /unattend:unattend.xml
{% endhighlight %}

Die Batch-Datei muss zusammen mit der unattend.xml in das Sysprep-Verzeichnis unter _C:\Windows\System32\Sysprep_ gelegt werden. Nach Ausführung der Batch-Datei beginnt direkt die Versiegelung (generalize) und anschließen startet Windows neu. Nun gilt es, anstelle des Neustarts, das System mit seinem Imaging-Tool zu booten (z.B. von USB oder PXE-Boot) und ein Image zu erstellen. Wird dieses Image später verteilt, wird beim ersten Booten direkt wie gewünscht der Sysprep-Vorgang weitergeführt.

Zusammenfassend noch einmal alle nötigen Schritte, um ein Master Image zu erstellen:


  1. Erstellen einer unattend.xml mit Windows SIM
  2. Installation von Windows
  3. Installation aller benötigter Treiber und Software (dies kann auch Server-Rollen wie z.B. Hyper-V beinhalten)
  4. Kopieren der unattend.xml in das Sysprep-Verzeichnis
  5. Ausführen von Sysprep mittels Batch-Datei oder manuell über cmd
  6. Abfangen des Bootvorgangs und Erstellung des Images


Die unattend.xml aus diesem Beispiel kann bei Bedarf gerne für eigene Zwecke verwendet und/oder angepasst werden. Das Administrator Passwort, welches vergeben wird, lautet in diesem Beispiel _Pa$$w0rd_.

Ein letzter kleiner Tipp: Die unattend.xml kann natürlich für einfache Änderungen auch schnell in einem Editor angepasst werden. Z.B. kann der `SkipRearm`-Wert zum Testen im Editor einfach auf 1 gesetzt werden und erst zur Image-Erstellung dann zu 0 geändert werden. Für weiterführende Anpassungen empfiehlt es sich jedoch, Windows SIM zu verwenden.
