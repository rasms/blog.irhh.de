---
author: Rasmus
comments: true
date: 2013-04-23 17:11:02+00:00
layout: post
slug: bginfo-registry-eintrag-fur-automatischen-start-per-batch-datei-hinzufuegen
title: 'BGInfo: Registry-Eintrag für automatischen Start per Batch-Datei hinzufügen'
wordpress_id: 1742
categories:
- BGInfo
- Registry
- Windows Client
- Windows Server
---

[BGInfo](http://technet.microsoft.com/de-de/sysinternals/bb897557.aspx) aus der [Sysinternals Suite](http://technet.microsoft.com/de-de/sysinternals/bb842062) ist ein sehr hilfreiches Tool, das auf keinem Server fehlen sollte. Gerade wenn man täglich per RDP auf vielen Servern unterwegs ist, hilft BGInfo dabei, die Übersicht zu behalten und die wichtigsten Informationen über den Server direkt im Auge zu haben. Über einen Eintrag in der Registry lässt es sich auch einfach automatisch starten, inkl. individuell angepassten Einstellungen. Möchte man den nötigen Eintrag nicht immer per Hand vornehmen, kann man sich einfach mit einer kleinen Batch-Datei helfen:

{% highlight powershell %}
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run /v BGInfo /t REG_SZ /d "%SYSTEMROOT%\bginfo\bginfo.exe /accepteula /i%SYSTEMROOT%\bginfo\conf.bgi /timer:0" /f
{% endhighlight %}




Dieser Befehl fügt der Registry einen Eintrag hinzu, der BGInfo mit der Konfigurations-Datei _conf.bgi_ ohne Verzögerung und ohne EULA-Bestätigung startet. Dafür muss die _bginfo.exe_ und die _conf.bgi_ unter _C:\Windows\bginfo_ liegen. Der Pfad lässt sich natürlich nach Bedarf ändern. Die Batch-Datei muss als Administrator ausgeführt werden.
