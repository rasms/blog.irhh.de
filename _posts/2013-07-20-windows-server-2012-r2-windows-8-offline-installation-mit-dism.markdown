---
author: Rasmus
comments: true
date: 2013-07-20 14:42:35+00:00
layout: post
slug: windows-server-2012-r2-windows-8-offline-installation-mit-dism
title: 'Windows Server 2012 (R2), Windows 8: Offline Installation mit DISM'
wordpress_id: 1828
categories:
- DISM
- Windows 8
- Windows Server 2012
- Windows Server 2012 R2
---

Mein Versuch Windows Server 2012 R2 Preview auf eine 60 GB SSD zu installieren, scheiterte daran, dass die SSD von dem Installationsmenü nicht erkannt wurde. Woran das lag, kann ich nicht genau sagen, ich vermute die Größe spielt hier eine Rolle, auch wenn laut [Systemanforderungen](http://technet.microsoft.com/en-us/library/dn303418.aspx) 32 GB ausreichend sein sollen. Zum Glück lässt sich Windows Server 2012 (R2) ebenso wie Windows 8 offline installieren, indem man mittels DISM die install.wim auf die Zielplatte anwendet. Natürlich benötigt man zur Durchführung eine funktionierende Windows Installation (z.B. in einem anderen Rechner oder ein Windows 8 To Go).

<!-- more -->
Im ersten Schritt muss die Festplatte mit Diskpart gelöscht und formatiert werden. Dafür startet man eine cmd mit Admin-Rechten und gibt folgendes ein, wobei x und y durch die entsprechende Nummer der Festplatte und Partition zu ersetzen sind.

{% highlight powershell %}
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

Anschließend muss der Festplatte über die Datenträgerverwaltung ein Laufwerksbuchstabe zugewiesen werden. Nun muss das .iso von Windows Server 2012 (R2) gemounted werden, alternativ kann man natürlich auch eine DVD, einen USB-Stick oder eine entpackte install.wim verwenden. Wichtig ist nur, dass die install.wim über einen Pfad direkt anzusprechen ist (In meinem Beispiel über Laufwerk _D_). Da in der install.wim von Windows Server immer mehrere Versionen enthalten sind, muss zuerst überprüft werden, welchen Index die gewünschte Version hat.

```
    dism /get-wiminfo /wimfile:d:\sources\install.wim
```

führt bei Windows Server 2012 R2 Preview zu folgender Ausgabe:

![get wim]({{ site.url }}/images/2013/getwim.png )

In meinem Beispiel möchte ich Windows Server 2012 R2 Datacenter mit GUI installieren, also brauche ich den _Index 4_. Zusätzlich benötigt man noch den Laufwerksbuchstaben der Zielfestplatte (in meinem Beispiel _G_, muss genau wie der Pfad zur install.wim natürlich entsprechend angepasst werden), um die install.wim auf die Festplatte zu entpacken und diese anschließend mittels BCDboot bootfähig zu machen.

```
    dism /apply-image /imagefile:d:\sources\install.wim /index:4 /applydir:g:\
    bcdboot g:\windows /s g:
```
![dism apply wim]({{ site.url }}/images/2013/dismapplywim.png )

Danach kann das von der Festplatte gestartet und das System eingerichtet werden. Natürlich lässt sich auf diese Weise auch Windows 8 installieren, z.B. auch auf einen USB-Stick oder eine USB-Festplatte als Windows 8 To Go.
