---
comments: true
layout: post
title: RDP-Verbindungen mittels IP-Filter der Windows Firewall sichern
---

Ich bin kein Freund davon, RDP-Verbindungen über Port-Freigaben zu ermöglichen, schließlich gibt es für externe Zugriffe VPN. Allerdings gibt es Situationen, wo man es leider nicht vermeiden kann. Aktuell hatte ich das Problem, dass ich aus dem Netz meiner Uni keine VPN-Verbindung nach Hause aufbauen kann, da die Sicherheits-Richtlinen dort sehr restriktiv sind. Auch die Variante, einen Port aus dem 50000-Bereich für RDP zu nutzen, funktioniert leider nicht, da im Uni-Netz nur bestimmte Ports freigegeben sind. Es blieb mir also nicht anderes übrig, als Port 3389 freizugeben. zum Glück kann man externe Zugriffe auf den Terminalserver sehr leicht einschränken, und das sogar mit Windows Bordmitteln. Unter den erweiterten Einstellungen der Windows Firewall lässt sich für Remote-Zugriffe ein IP-Adressbereich angeben. Dort einfach den Adressbereich der Uni eingetragen (das lokale Subnetz darf natürlich auch nicht vergessen werden) und schon hat man die Sicherheit der RDP Freigabe deutlich verbessert. Natürlich können immer noch unbefugte Zugriffe aus dem Uni-Netz erfolgen und gegen Sicherheitslücken in Windows schützt dies auch nicht zwangsläufig, aber besser als Port 3389 einfach für beliebige Zugriffe freizugeben, ist es alle mal...


![RDP IP-Filter]({{ site.url }}/images/2013/rdp_ipfilter1.png )
