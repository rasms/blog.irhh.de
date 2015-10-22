---
author: Rasmus
comments: true
date: 2014-01-09 20:00:00+00:00
layout: post
slug: vmm-2012-erstellung-eines-vm-templates-fuer-generation-2-vms
title: 'VMM 2012: Erstellung eines VM-Templates für Generation 2 VMs'
categories:
- VMM 2012
- System Center
- Windows Server 2012 R2
- Windows 8.1
- PowerShell
---

Nach der Migration aller Hyper-V Hosts auf Windows Server 2012 R2 habe ich nun mit der Erstellung von VM Templates in VMM 2012 R2 begonnen. Die Windows 8.1 und Windows Server 2012 R2 Templates sollen dabei natürlich Hyper-V VMs der zweiten Generation erzeugen. Wie sonst auch, habe ich zuerst die Generation 2 VM direkt auf einem Host im Hyper-V Manager erstellt, um anschließend in VMM aus der VM ein Template zu erstellen. Wie sich herausstellte hat VMM 2012 R2 leider aktuell wohl noch ein Problem mit Generation 2 VMs, so dass die Template-Erstellung immer mit Error 23352 <i>(VMM cannot find the device or this device is not valid for a boot device.)</i> abbricht. Eine ausführliche Diskussion und Workarounds zu diesem Probleme finden sich im [TechNet Forum](http://social.technet.microsoft.com/Forums/systemcenter/en-US/917c90d5-f7f4-454c-825b-fe0f28592978/cannot-create-vm-from-gen2-template?forum=virtualmachingmgrhyperv) und [hier](http://www.rickygao.com/scvmm-2012-r2-unable-to-create-vm-based-on-generation-2-vm-template/). Nach mehreren Versuchen hat es bei mir durch eine Kombination der unter den Links genannten Workarounds geklappt. Ich bin dazu wie folgt vorgegangen:
<!-- more -->


### 1. Installation der VM mit VMM

Erster wichtiger Punkt ist die Installation der VM. Die Installation muss zwingend mit VMM durchgeführt werden. Wird die VM über den Hyper-V Manager erstellt, ist es später nicht möglich, ein Template aus ihr zu erstellen. Bei der Erstellung der VM über VMM muss außerdem bei der Hardware Konfiguration unter dem Punkt <i>Firmware</i> der <i>sichere Start</i> deaktiviert werden.

![get wim]({{ site.url }}/images/2014/vmmgen2_1.png )

Nach der Installation kann wie gewohnt die VM konfiguriert werden, also z.B. Software installiert werden, etc.

### 2. Ändern der Bootreihenfolge in Hyper-V

Direkt vor der Erstellung des Templates muss die Bootreihenfolge der VM geändert werden. Dafür zuerst die VM ausschalten und anschließend über den Hyper-V Manager die Einstellungen der VM öffnen. Unter <i>Firmware</i> muss die Bootreihenfolge geändert werden, <i>Datei (bootmgfw.efi)</i> muss von der ersten Position nach unten verschoben werden.

![get wim]({{ site.url }}/images/2014/vmmgen2_2.png )

Im Anschluß kann das VM-Template wie gewohnt über die VMM-Konsole erstellt werden.

### 3. Anpassen des VM-Templates mit PowerShell

Ist die Erstellung (hoffentlich) erfolgreich abgeschlossen, muss die Bootreihenfolge diesmal über PowerShell angepasst werden. Dafür die VMM-PowerShell öffnen und das Template mit folgendem Befehl anpassen:

{% highlight PowerShell %}
get-scvmtemplate -name "Template-Name" | set-scvmtemplate -FirstBootDevice “SCSI,0,0”
{% endhighlight %}

Anschließend können bei Bedarf noch die regionalen Einstellungen des Templates angepasst werden:

{% highlight PowerShell %}
    $template = Get-SCVMtemplate | where {$_.Name -eq "VMtemplateName"}
    $settings = $template.UnattendSettings
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/UserLocale","de-DE")
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/SystemLocale","de-DE")
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/UILanguage","de-DE")
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/InputLocale","de-DE")
    Set-SCVMTemplate -VMTemplate $template -UnattendSettings $settings
{% endhighlight %}
