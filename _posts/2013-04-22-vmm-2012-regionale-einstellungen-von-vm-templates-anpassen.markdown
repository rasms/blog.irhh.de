---
author: Rasmus
comments: true
date: 2013-04-22 17:46:10+00:00
layout: post
slug: vmm-2012-regionale-einstellungen-von-vm-templates-anpassen
title: 'VMM 2012: Regionale Einstellungen von VM-Templates anpassen'
wordpress_id: 1502
categories:
- Hyper-V
- System Center
- VMM 2012
---

Wer mit VMM 2012 schon einmal VM-Vorlagen erstellt hat, die auf einer deutschen VM basieren, kennt das Problem: Erstellt man neue VMs mit Hilfe der Vorlage, stehen die regionalen Einstellungen auf Englisch (USA). Möchte man die Lokalisierung nicht in jeder neuen VM manuell anpassen, gibt es zwei Möglichkeiten:

<!-- more -->
### 1. unattend.xml



Mit Hilfe des Windows System Image Manager (Windows SIM), enthalten in [WAIK](http://www.microsoft.com/de-de/download/details.aspx?id=5753) oder [ADK](http://www.microsoft.com/de-de/download/details.aspx?id=30652), kann man recht einfach eine unattend.xml erstellen, die die angepassten Lokalisierungseinstellungen enthält. Natürlich kann man an dieser Stelle auch noch weitere Einstellungen und Anpassungen vornehmen, daher eignet sich diese Methode vor allem, wenn man viele Einstellungen vorzunehmen hat und ohnehin eine unattend.xml verwendet.
Alternativ kann man auch einfach mit einem Editor eine unattend.xml mit folgendem Inhalt erstellen:


{% highlight xml %}
    <?xml version="1.0" encoding="utf-8"?>
    <unattend xmlns="urn:schemas-microsoft-com:unattend">
        <settings pass="oobeSystem">
            <component name="Microsoft-Windows-International-Core" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
                <InputLocale>de-DE</InputLocale>
                <SystemLocale>de-DE</SystemLocale>
                <UILanguage>de-DE</UILanguage>
                <UILanguageFallback>en-US</UILanguageFallback>
                <UserLocale>de-DE</UserLocale>
            </component>
        </settings>
    </unattend>
{% endhighlight %}



Die unattend.xml kann dann dem VM-Template über _Eigenschaften -> Betriebssystemkonfiguration -> Skripts -> Antwortdatei_ hinzugefügt werden.



### 2. PowerShell



Einfacher und schneller ist es, die Regions-Anpassungen über die VMM-PowerShell vorzunehmen:



{% highlight PowerShell %}
    $template = Get-SCVMtemplate | where {$_.Name -eq "VMtemplateName"}
    $settings = $template.UnattendSettings
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/UserLocale","de-DE")
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/SystemLocale","de-DE")
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/UILanguage","de-DE")
    $settings.Add("oobeSystem/Microsoft-Windows-International-Core/InputLocale","de-DE")
    Set-SCVMTemplate -VMTemplate $template -UnattendSettings $settings
{% endhighlight %}



Den entsprechenden KB-Artikel gibt's [hier](http://support.microsoft.com/kb/2709539).
