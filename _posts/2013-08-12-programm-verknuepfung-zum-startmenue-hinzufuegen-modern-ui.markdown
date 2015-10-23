---
comments: true
layout: post
title: Programm-Verknüpfung zum Startmenü hinzufügen (Modern UI)
---

Um bei Windows 8(.1) oder Windows Server 2012 (R2) eigene Programme zum Startmenü hinzuzufügen, muss man für diese zuerst eine Verknüpfung erstellen. Diese kann neben dem Programmpfad auch noch weitere Infos enthalten, z.B. kann man einfach eine bestimmte Access Datenbank mit Access öffnen. Der Verknüpfung kann auch ein eigenes Icon zugewiesen werden. Die Verknüpfung muss nur noch unter

{% highlight text %}
C:\Users\Username\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\
{% endhighlight %}

abgelegt werden und wird dann auf dem Startmenü angezeigt.

![custom startmenu tile]({{ site.url }}/images/2013/verknuepfung.png )
