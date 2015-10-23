---
comments: true
layout: post
title: .NET Framework 3.5 unter Windows Server 2012 / Windows 8 installieren
---

Für das .NET Framework 3.5 sind bei Windows Server 2012 bzw. bei Windows 8 die Binärdaten nicht mehr enthalten. Dass bedeutet, dass bei der Installation von .NET 3.5 die für die Installation benötigten Daten von Windows Update heruntergeladen werden müssen. Leider führt das in Verbindung mit WSUS häufig zu Problemen, nämlich genau dann, wenn die GPO _"Einstellungen für die Installation optionaler Komponenten und die Reparatur von Komponenten angeben"_ unter _Computerkonfiguration\Administrative Vorlagen\System_ nicht aktiviert ist. In der Folge bricht die Installation mit den Error-IDs **0x800F0906** oder **0x800F0907** ab. Natürlich ist langfristig die Anpassung der GPO die sinnvollste Lösung, ist dies jedoch nicht möglich oder nicht gewollt, lässt sich das .NET Framework 3.5 auch einfach und schnell über Kommandozeile vom Installationsmedium installieren:


{% highlight text %}
dism /online /enable-feature /featurename:NetFx3 /All /Source:<Laufwerk>:\sources\sxs /LimitAccess
{% endhighlight %}


Einfach _&lt;Laufwerk&gt;_ gegen den Laufwerksbuchstaben der eingelegten oder gemounteden Windows DVD ersetzen und die Installation klappt schnell und problemlos.

Die ausführlichen Informationen finden sich bei MS unter [KB 2734782](http://support.microsoft.com/kb/2734782/de).
