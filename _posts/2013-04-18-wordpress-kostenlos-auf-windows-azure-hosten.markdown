---
author: Rasmus
comments: true
date: 2013-04-18 12:55:26+00:00
layout: post
slug: wordpress-kostenlos-auf-windows-azure-hosten
title: Wordpress kostenlos auf Windows Azure hosten
wordpress_id: 1052
categories:
- Azure
- Public Cloud
- WordPress
---

Windows Azure biete die Möglichkeit bis zu 10 kostenlose Websites bereitzustellen. Diese Seiten sind öffentlich erreichbar, allerdings sind die Ressourcen sehr eingeschränkt. Dennoch eignen sie sich wunderbar für Blogs oder Projekte, bei denen nicht allzu viel Traffic zu erwarten ist und die Kosten möglichst gering ausfallen sollen.
<!-- more -->
Einen Haken gibt es jedoch: Es nicht möglich, einer Seite eine Domain zuzuweisen, solange sie im kostenlosen Modus ausgeführt wird. Trägt man bei seinem Domain-Provider die Azure-Site als Ziel ein, z.B. über eine Weiterleitung oder einen DNS CNAME-Eintrag, bekommt man nur eine Fehlermeldung. Es ist erforderlich, dass die Domain im Azure-Portal registriert und einer Site zugewiesen wird, damit diese Site über den Domainnamen erreichbar ist.

Was kann man also tun, wenn man trotzdem eine kostenlose Azure-Website unter seiner eigenen Domain erreichbar machen möchte? Die Antwort ist denkbar einfach: Weiterleiten! Eine Möglichkeit ist, eine HTTP-Weiterleitung auf seinem eigenen Webspace einzurichten, die automatisch auf die Azure-Website weiterleitet.

{% highlight HTML %}
<html xmlns="http://www.w3.org/1999/xhtml">
  <head>
    <meta http-equiv="refresh" content="0; URL=http://irhh.azurewebsites.net/">
    <title>irhh.de</title>
  </head>
  <body>
    Sollten Sie nicht automatisch weitergeleitet werden, klicken Sie <a href="http://irhh.azurewebsites.net">hier</a>.
  </body>
</html>
{% endhighlight %}

Dies erfordert aber einen eigenen Webspace, der wieder Kosten verursacht. Eine kostenlose Möglichkeit zur Weiterleitung bieten Link-Shortening-Dienste, wie z.B. [bit.ly](http://bit.ly). Dort kann man ein kostenloses Konto erstellen und einen Shortlink zu seiner Azure-Website erstellen. Diesen Link trägt man dann bei seinem Anbieter als Weiterleitungsziel für seine Domain ein, und fertig! Bei Aufruf der Domain wird man nun automatisch auf die Azure-Website weitergeleitet, ohne Fehlermeldung oder zusätzlich Kosten.

Möchte man mehrere Wordpress-Installationen parallel im kostenlosen Azure Modus ausführen, muss man darauf achten, dass sie alle dieselbe MySQL-Datenbank benutzen. Dafür muss bei jeder Wordpress-Seite ein eigenes `table_prefix` gesetzt werden. [Hier gibts eine Anleitung.](http://ntotten.com/2012/12/20/hosting-multiple-wordpress-sites-for-free-on-windows-azure/)
