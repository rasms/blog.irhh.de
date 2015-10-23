---
comments: true
layout: post
title: 'WSUS: HTTP-Fehler bei der Synchronisation'
---

Bei neuen Installation von WSUS auf Windows Server 2008 R2 bricht die erste Synchronisation mitunter mit einem HTTP-Fehler ab. Lassen sich fehlerhafte Firewall-Einstellungen ausschließen, hilft zum Glück meisten das Update [KB2734608](http://support.microsoft.com/kb/2734608/en-us). Nach der Installation muss die Synchronisation nur noch manuell angeworfen werden, z.B. durch den WSUS Server Configuration Wizard und sollte dann erfolgreich durchlaufen.
