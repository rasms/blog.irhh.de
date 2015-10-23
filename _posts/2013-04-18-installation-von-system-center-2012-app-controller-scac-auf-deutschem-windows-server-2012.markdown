---
comments: true
layout: post
title: Installation von System Center 2012 App Controller (SCAC) auf deutschem Windows Server 2012
---

Leider hat MS mit erscheinen des SP1 für VMM 2012 ja das Self-Service Portal gestrichen. Ersetzt wird es durch den System Center App Controller. Ansich ist die Installation von SCAC eine Sache von einigen Minuten, wäre da nicht ein Bug in der deutschen Version: Probiert man SCAC auf einem deutschsprachigen Windows Server 2012 zu installieren, bricht die Installation bei der IIS-Einrichtung ab, das Log gibt folgende Fehlermeldung als Ursache aus:

{% highlight text %}
Fatal 'ACL-Einträge werden hinzugefügt.' step failed in install task 'ACL-Einträge werden hinzugefügt.', starting rollback.
{% endhighlight %}

Nach einigem Suchen und Probieren deutete alles daraufhin, dass diese Problem nur auf eine Weise zu lösen ist: Installation von SCAC auf einem englischsprachigen Windows Server 2012. Auch die Nachinstallation der englischen Sprachpakete auf dem deutschen Server 2012 brachte keinen Erfolg, also setzte ich gezwungenermaßen einen englischen Server 2012 auf und siehe da: Die Installation lief problemlos in wenigen Minuten durch und läuft seit dem einwandfrei. Wer also plant, mit dem SCAC das Self-Service Portal von VMM zu ersetzen, und dafür nicht extra einen eigenen Server bereitstellen möchte, sollte bereits von der VMM Installation überlegen, ob er nicht lieber einen englischen Windows Server 2012 verwendet...

**Edit:** Das Problem wurde mit dem [Update Rollup 2 für System Center 2012 SP1](http://blogs.technet.com/b/scvmm/archive/2013/04/11/update-rollup-2-for-system-center-2012-service-pack-1-app-controller-updates.aspx) behoben. Um SCAC 2012 SP1 auf einem deutschen Windows Server 2012 zu installieren, muss bei der Installation der Haken bei "Suchen Sie mit Hilfe von Microsoft Update nach Updates für App Controller-Setup" gesetzt werden, dann läuft die Installation ohne Probleme durch.
