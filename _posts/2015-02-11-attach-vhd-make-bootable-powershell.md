---
comments: true
date: 2015-02-11
layout: post
slug: attach-vhd-make-bootable-powershell
title: Attach a VHD and make it bootable with PowerShell
---

Sometimes you need to mount a VHD to a Windows Server, make it bootable and set it as the default boot. While this process is not that difficult, when you have to do it several times again, e.g. on several hosts with the same configuration, it would be nice to automate it.

If you're in a position where you have to install MOCs (Microsoft Official Courses) for classrooms setups, you might know that there are some MOCs, where you have to boot a special host-VHD, if you have to install a full classroom, this isn't that funny. To make live easier, I wrote this little PowerShell script, which mounts the host-VHD, makes it bootable and then sets it as default boot. To use the same script on two hosts (if for this MOC two hosts are needed), you will be asked at the beginning, on which host you are on.

Before you can use this script, you have to change the path to the VHD you want to mount and you should change the boot-description on the last line.

{% gist 444a5c974bf116c3314b %}
