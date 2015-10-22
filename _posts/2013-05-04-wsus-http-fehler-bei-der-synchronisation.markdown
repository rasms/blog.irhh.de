---
author: Rasmus
comments: true
date: 2013-05-04 21:54:50+00:00
layout: post
slug: wsus-http-fehler-bei-der-synchronisation
title: 'WSUS: HTTP-Fehler bei der Synchronisation'
wordpress_id: 1761
categories:
- Windows Server 2008 R2
- WSUS
---

Bei neuen Installation von WSUS auf Windows Server 2008 R2 bricht die erste Synchronisation mitunter mit einem HTTP-Fehler ab. Lassen sich fehlerhafte Firewall-Einstellungen ausschließen, hilft zum Glück meisten das Update [KB2734608](http://support.microsoft.com/kb/2734608/en-us). Nach der Installation muss die Synchronisation nur noch manuell angeworfen werden, z.B. durch den WSUS Server Configuration Wizard und sollte dann erfolgreich durchlaufen.
