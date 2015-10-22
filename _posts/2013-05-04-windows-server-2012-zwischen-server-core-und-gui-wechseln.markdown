---
author: Rasmus
comments: true
date: 2013-05-04 22:21:08+00:00
layout: post
slug: windows-server-2012-zwischen-server-core-und-gui-wechseln
title: 'Windows Server 2012: Zwischen Server Core und GUI wechseln'
wordpress_id: 1764
categories:
- PowerShell
- Windows Server 2012
---

Windows Server 2012 biete bekanntlich die sehr interessante Möglichkeit, die GUI auf einer Server Core Installation nach zu installieren, bzw. bei einer vollständigen Installation nachträglich zu deinstallieren. So kann man einen Server z.B. für die Erstkonfiguration mit grafischer Oberfläche installieren und anschließend deinstalliert man die GUI für den Betrieb einfach. (Beispielsweise zur Einsparung von Ressourcen und Verminderung von Angriffsfläche und Update-Bedarf.)


### 1. Installation von GUI auf Server Core



Um auf einer Server-Core-Installation von Windows Server 2012 die GUI zu installieren benötigt man eine Installations-DVD / -USB-Stick oder ein eingehängtes Image.

Zuerst erstellt man sich einen neuen Ordner um anschließend mittels _dism_ das _install.wim_ zu mounten:

{% highlight powershell %}
mkdir c:\mount
dism.exe /mount-image /ImageFile:d:\sources\install.wim /Index:4 /Mountdir:c:\mount /readonly
{% endhighlight %}

Danach startet man PowerShell und installiert die GUI:
{% highlight powershell %}
PowerShell.exe
Import-Module ServerManager
Install-WindowsFeature -IncludeAllSubfeature User-Interfaces-Infra -Source:c:\mount\windows
{% endhighlight %}




### 2. Deinstallation der GUI 



Die Deinstallation der GUI mit Hilfe von PowerShell ist noch einfacher:

{% highlight powershell %}
Import-Module ServerManager
Uninstall-WindowsFeature User-Interfaces-Infra
{% endhighlight %}
