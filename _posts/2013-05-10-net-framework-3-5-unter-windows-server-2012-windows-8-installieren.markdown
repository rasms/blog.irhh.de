---
author: Rasmus
comments: true
date: 2013-05-10 06:52:27+00:00
layout: post
slug: net-framework-3-5-unter-windows-server-2012-windows-8-installieren
title: .NET Framework 3.5 unter Windows Server 2012 / Windows 8 installieren
wordpress_id: 1773
categories:
- Windows 8
- Windows Server 2012
---

Für das .NET Framework 3.5 sind bei Windows Server 2012 bzw. bei Windows 8 die Binärdaten nicht mehr enthalten. Dass bedeutet, dass bei der Installation von .NET 3.5 die für die Installation benötigten Daten von Windows Update heruntergeladen werden müssen. Leider führt das in Verbindung mit WSUS häufig zu Problemen, nämlich genau dann, wenn die GPO "_Einstellungen für die Installation optionaler Komponenten und die Reparatur von Komponenten angeben" _unter _Computerkonfiguration\Administrative Vorlagen\System_ nicht aktiviert ist. In der Folge bricht die Installation mit den Error-IDs **0x800F0906** oder **0x800F0907** ab. Natürlich ist langfristig die Anpassung der GPO die sinnvollste Lösung, ist dies jedoch nicht möglich oder nicht gewollt, lässt sich das .NET Framework 3.5 auch einfach und schnell über Kommandozeile vom Installationsmedium installieren:

<!-- more -->

```
dism /online /enable-feature /featurename:NetFx3 /All /Source:<Laufwerk>:\sources\sxs /LimitAccess
```


Einfach _<Laufwerk>_ gegen den Laufwerksbuchstaben der eingelegten oder gemounteden Windows DVD ersetzen und die Installation klappt schnell und problemlos.

Die ausführlichen Informationen finden sich bei MS unter [KB 2734782](http://support.microsoft.com/kb/2734782/de).
