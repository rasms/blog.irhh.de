---
author: Rasmus
comments: true
date: 2013-12-30 09:30:00+00:00
layout: post
slug: vmm-2012-migration-aller-vms-eines-standalone-hyper-v-hosts
title: 'VMM 2012: Migration aller VMs eines standalone Hyper-V Hosts'
categories:
- VMM 2012
- System Center
- Windows Server 2012
- PowerShell
---

Im Zuge der Migration einiger standalone Hyper-V Hosts auf Windows Server 2012 R2 stand ich letztens vor der Aufgabe, viele VMs von einem Host auf einen anderen zu migrieren. Da die Migration der Hosts mit einem Versionssprung von Windows Server 2008 R2 auf 2012 R2 verbunden war, war es am einfachsten die VMs mit VMM 2012 R2 auf mehrere temporäre Hyper-V Hosts zu migrieren, um sie nach der Host-Migration wieder mit VMM auf den Host zurückzukopieren. Das Problem war also, viele VMs von einem standalone Hyper-V Host auf einen anderen zu migrieren, das ganze natürlich möglichste automatisiert und eine VM nach der anderen.


<!-- more -->


Da ich auf die schnelle keine Lösung im Internet gefunden habe, habe ich selber ein kleines PowerShell-Script geschrieben, was das Problem löst. Mit dem Script werden nacheinander alle ausgeschalteten VMs eines Hosts auf einen anderen Host kopiert.
Das Script muss aus der VMM-PowerShell gestartet werden und wurde mit VMM 2012 R2 auf Windows Server 2012 R2 erfolgreich gestestet.


{% gist 9900f92ad18e86c88be4 %}
