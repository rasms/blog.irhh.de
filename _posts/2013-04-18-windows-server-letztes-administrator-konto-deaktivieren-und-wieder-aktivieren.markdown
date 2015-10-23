---
comments: true
layout: post
title: 'Windows Server: Deaktiviertes Administrator-Konto wieder aktivieren'
---

Da hat MS mal wieder nicht mitgedacht: Leider ist es ohne weiteres möglich, bei Windows Server 2008 R2 das letzte und einzige Administrator-Konto zu deaktivieren, und das ganz ohne Warnung.
Es endet natürlich darin, dass man sich selber von seinem Server aussperrt...

Zum Glück habe ich im Rachfahl Technikblog eine so schnelle wie einfache [Lösung](http://technikblog.rachfahl.de/losungen/windows-server-2008-administrator-passwort-vergessen/) gefunden:
Man bootet den Server einfach von der Installations-DVD und startet die Wiederherstellungskonsole. Dort gibt man folgende Befehle ein, der Laufwerksbuchstabe kann dabei variieren, mit `dir` lässt sich aber einfach überprüfen, ob man auf dem richtigen Laufwerk ist.

{% highlight text %}
d:
cd windowssystem32
rename Utilman.exe Utilman.bak
copy cmd.exe Utilman.exe
{% endhighlight %}

Anschließend bootet man den Server neu, diesmal nicht von DVD. Auf dem Anmeldebildschirm kann man jetzt durch einen Klick links unten auf das Symbol für die Eingabehilfen (Alternative: _Windowstaste + u_) eine Kommandozeile öffnen. Über folgenden Befehl kann man das Administrator-Konto wieder aktivieren, alternativ kann man auch [einen neuen User anlegen und zum Administrator machen...](http://technikblog.rachfahl.de/losungen/windows-server-2008-administrator-passwort-vergessen/)

{% highlight text %}
net user administrator /active:yes
{% endhighlight %}

Nun startet man noch ein mal neu, diesmal wieder von DVD, und macht die Änderungen in der Systemwiederherstellungskonsole wieder rückgängig:

{% highlight text %}
d:
cd windowssystem32
copy Utilman.bak Utilman.exe
del Utilman.bak
{% endhighlight %}

Nun kann man ein letztes Mal neu starten, und sich wieder wie gewohnt anmelden. Abschließend bleiben zwei Erkenntnisse: Zum einen stellt sich die Frage, warum MS es überhaupt zulässt, das man das einzige und letzte Konto deaktivieren kann?... Zumindest eine Abfrage und/oder Warnung wäre an dieser stellen angebracht. Und zweitens macht einem das ganze wieder Bewusst, wie erschreckend schnell und einfach man ohne jede Rechte Admin-Zugang zu einem Server erlangen kann, wenn man physischen Zugriff auf ihn hat.
