---
author: Rasmus
comments: true
date: 2013-09-01 16:23:48+00:00
layout: post
slug: wordpress-3-6-video-embed-und-ie10
title: Wordpress 3.6 Video embed und IE10
wordpress_id: 2091
categories:
- WordPress
---

Seit Wordpress 3.6 ist ja möglich, Videos direkt über Wordpress einzubetten, ohne Plugins, etc. Leider hatte ich damit dann direkt wieder ein Problem, genauer gesagt der Internet Explorer 10: Während das Video in allen anderen getesteten Browsern ohne Probleme lief, tat sich im IE10 rein gar nichts. Ein Blick in die IE-Entwicklertools brachte einen ersten Verdacht zu Tage: Als Type wurde bei dem mp4-Video vom IE text/html angezeigt, was offensichtlich falsch ist. Weitere Recherche ergab, dass Wordpress als Mediaplayer [MediaElement.js](http://mediaelementjs.com) nutzt. Dort findet man schnell, dass bei Apache-Webservern für Videos der MIME-Type manuell über die .htaccess definiert werden muss.
{% highlight apache %}
AddType video/ogg .ogv
AddType video/mp4 .mp4
AddType video/webm .webm
{% endhighlight %}

Mit diesen Ergänzungen in der .htaccess-Datei läuft das Video dann auch im IE10 ohne Probleme.
