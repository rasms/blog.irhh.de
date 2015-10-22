---
author: Rasmus
comments: true
date: 2013-08-12 14:51:33+00:00
layout: post
slug: programm-verknuepfung-zum-startmenue-hinzufuegen-modern-ui
title: Programm-Verknüpfung zum Startmenü hinzufügen (Modern UI)
wordpress_id: 2004
categories:
- Windows 8
- Windows 8.1
- Windows Server 2012
- Windows Server 2012 R2
---

Um bei Windows 8(.1) oder Windows Server 2012 (R2) eigene Programme zum Startmenü hinzuzufügen, muss man für diese zuerst eine Verknüpfung erstellen. Diese kann neben dem Programmpfad auch noch weitere Infos enthalten, z.B. kann man einfach eine bestimmte Access Datenbank mit Access öffnen. Der Verknüpfung kann auch ein eigenes Icon zugewiesen werden. Die Verknüpfung muss nur noch unter

```
C:\Users\Username\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\
```

abgelegt werden und wird dann auf dem Startmenü angezeigt.

![get wim]({{ site.url }}/images/2013/verknuepfung.png )
