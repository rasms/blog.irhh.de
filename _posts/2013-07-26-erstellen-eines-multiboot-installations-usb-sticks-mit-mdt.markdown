---
comments: true
layout: post
title: Erstellen eines Multiboot-Installations-USB-Sticks mit MDT
---

Trotz Deployment-Lösungen wie WDS oder SCCM kommt es immer wieder mal vor, dass man ein System direkt von DVD bzw. USB-Stick installieren muss, z.B. bei Kundeneinsätzen. Dank des [Windows 7 USB/DVD Download Tools](http://www.microsoftstore.com/store/msusa/html/pbPage.Help_Win7_usbdvd_dwnTool) ist das Erstellen von Bootsticks zur Betriebssysteminstallation nur noch eine Sache von wenigen Klicks. Leider benötigt man in der Praxis meisten immer gerade das Betriebssystem, welche man nicht als Installations-Stick verfügbar hat. Warum also nicht einen USB-Stick erstellen, der alle benötigten Windows Betriebssysteme enthält?

Wir benötigen als erstes einen USB-Stick, der für die ganzen verschiedenen Installationsdateien groß genug ist. Je nach dem, wie viele Betriebssysteme wir später von einem Stick installieren wollen, sind hier 16 GB oder besser 32 GB angebracht. Dann benötigen wir das [Microsoft Deployment Toolkit (MDT)](http://www.microsoft.com/en-us/download/details.aspx?id=25175) sowie das [Windows Assessment and Deployment Toolkit (ADK)](http://www.microsoft.com/de-de/download/details.aspx?id=30652). Zu guter Letzt benötigen wir natürlich die Installationsmedien der Betriebssysteme, die wir später von dem Stick installieren wollen, ob als ISO oder DVD ist hierbei egal, wichtig ist, dass wir sie später mounten können.


## 1. Installation und Konfiguration


Im ersten Schritt installieren wir ADK und MDT. Beim ADK ist darauf zu achten, dass die Punkte _Bereitstellungstools_ und _Windows PE_ installiert werden, alle anderen Features benötigen wir hier nicht.


![Windows ADK]({{ site.url }}/images/2013/01_adk.png )


Nach der Installation starten wir MDT _(Deployment Workbench)_ und legen als erstes eine neue _Deployment Share_ an. Hierbei sollte man bedenken, dass diese später recht groß werden kann, also sollte man ein Laufwerk wählen, auf dem noch ausreichend Speicher frei ist. Der Rest kann mit den Standard-Einstellungen installiert werden.


## 2. OS Import


Nun importieren wir alle Betriebssysteme, die wir später von dem Stick installieren möchten. Dafür mounten wir jeweils die ISO und klicken mit einen Rechtsklick auf _Operating Systems_ und dann auf _Import Operating Systems_. Die Einstellungen für den folgenden Import-Wizard sind selbsterklärend, allerdings sollten wir darauf achten den _Destination directory name_ möglichst kurz zu wählen, da wir sonst später ein Problem mit zu langen Dateipfaden bekommen.


![Import OS - Windows ADK]({{ site.url }}/images/2013/04_importos.png )


Nach dem OS-Import können wir bei Bedarf auch noch benötigte Treiber und/oder Software importieren. Da ich hier aber einen universell einsetzbaren Bootstick erstellen möchte, der jeweils nur eine saubere Neuinstallation ermöglicht, überspringe ich diese Punkte.


## 3. Erstellen von Tasksequenzen


Nun müssen wir für jedes OS, welches später installierbar sein soll, eine eigene _Task Sequence_ erstellen. Soll heißen: Nur was wir hier als Task Sequence anlegen, wird später auch in der Installationsauswahl auftauchen!


![New Tasksequence]({{ site.url }}/images/2013/06_newtaskseq.png )


Die ID zählen wir einfach hoch, den Namen wählen wir so, dass er selbsterklärend und eindeutig ist. Der Name ist auch der, den wir später im Installationsmenü sehen.

![New Tasksequence 2]({{ site.url }}/images/2013/06a_newtaskseq2.png )


Als Template wählen wir bei einem Server OS das _Server-Template_ und bei einem Client OS entsprechend das _Client Template_. In den nächsten Punkten müssen wir noch das passende OS auswählen, angeben ob wir einen Product Key hinzufügen wollen, Benutzername/Organisation eintragen und festlegen, ob wir hier schon ein Admin-Passwort vergeben wollen, oder erst später.


![New Tasksequence 3]({{ site.url }}/images/2013/06b_newtaskseq3.png)


## 4. Erstellung des Bootmediums


Bevor wir nun mit der Erstellung des Boot-Mediums beginnen können, müssen wir noch die Deployment Share updaten. Also mit einem Rechtsklick auf die _Deployment Share_ und dann _Update Deployment Share_.

![Update Deployment Share]({{ site.url }}/images/2013/08_updatedeploymentshare.png )


Nun können wir das Boot-Medium erstellen, in dem wir _Advanced Configuration_ → _Media_ → _New Media_ auswählen. Als Pfad sollten man hier einen Ordner außerhalb des Deployment Share-Ordners wählen, auch hier gilt: Darauf achten, dass genügend Speicherplatz verfügbar ist. Außerdem ist es wichtig, als _Selection Profile_ "Everything" auszuwählen.

![Build new Media]({{ site.url }}/images/2013/09_newmedia.png )

Die Erstellung des Mediums geht jetzt sehr schnell, um genau zu sein, zu schnell. Im angegebenen Ordner werden zuerst nur kleine konfigurations Files angelegt. Um das eigentliche Medium zu erstellen, müssen wir die Erstellung erneut anschmeißen, in dem wir mit einem Rechtsklick auf das Medium klicken und _Update Media Content_ auswählen. Dieser Schritt dauert nun deutlich länger als zuvor, da hier alle Installationsdateien doppelt kopiert werden.


![Update Media]({{ site.url }}/images/2013/11_updatemedia.png)


Nach Abschluss des Vorgangs sollte in unserem Ordner ein _LiteTouchMedia.iso_ sowie ein Ordner _Content_ zu finden sein. Der Ordner _Content_ sollte in der Größe in etwa dem ISO entsprechen.


## 5. Erstellen des USB-Sticks


Wir haben nun zwei Möglichkeiten, den Bootstick zu erstellen. Die erste ist, dass _LiteTouchMedia.iso_ einfach mit dem Windows 7 USB/DVD Download Tool auf den USB-Stick zu spielen. Das ganze endet zwar mit einer Fehlermeldung, die kann aber einfach ignoriert werden, es funktioniert trotzdem.

Für Variante zwei müssen wir den USB-Stick zuerst mit _Diskpart_ löschen und dort eine bootfähig Partition erstellen:

{% highlight text %}
    diskpart
    lis dis
    sel dis x
    lis par
    sel par y
    delete partition
    clean
    create partition primary
    format fs=ntfs quick
    active
    exit
{% endhighlight %}

Anschließend kopieren wir den gesamten Inhalt des Ordners Content direkt auf den USB-Stick.


## 6. Test


Natürlich möchten wir unseren USB-Stick jetzt testen. Dabei sollte man beachten, dass es während des gesamten Installationsdialogs keine Möglichkeit gibt, die Zielfestplatte anzugeben. D.h.: Es wird immer automatisch auf die primäre Festplatte im Rechner installiert! Hat man also in einem Rechner noch eine Festplatte frei und möchte auf dieser die Installation testen, sollten man vorher alle anderen Festplatte physisch trennen, ansonsten geht das schnell nach Hinten los!

Wir können jetzt also den USB-Stick an einem Rechner testen, alternativ können wir auch das ISO testen, indem wir einfach eine VM erstellen und von dem ISO booten. Zuerst muss man zwischen x86 und x64 wählen, hier entscheidet sich bereits, welche Systeme man im nächsten Schritt zur Auswahl hat. Wählt man x86, hat man nur die 32-Bit Betriebssysteme zur Auswahl, bei x64 entsprechend nur die 64-Bit Systeme. Nach ein paar Klicks sollte dann folgende Auswahl erscheinen, wegen der wir das alles hier gemacht haben:


![Test]({{ site.url }}/images/2013/12_test.png )
