---
comments: true
layout: post
title: Statische Website auf Amazon S3 hosten
wordpress_id: 2082

---

In einer kleinen Serie von Posts werde ich mal erklären, wie man eine statische Website auf Amazon S3 hostet, mit [Amazon CloudFront beschleunigt](http://blog.irhh.de/2013/statische-webinhalte-durch-amazon-cloudfront-bereitstellen/), für die Performance-Optimierung Inhalte komprimiert bereitstellt und das Browser-Caching steuert und wie man mit Amazon Route 53 die DNS-Auflösung an AWS übergibt. Ich beginne in diesem Post mit S3:


Nach der obligatorischen Registrierung bei [AWS](http://aws.amazon.com/de/) kann man auf die [AWS Konsole](https://console.aws.amazon.com/) zugreifen und dort alle benötigten Services einrichten. Für die Einrichtung von S3 klickt man entsprechend auf S3. Amazon S3 benutzt zur Organisation sog. Buckets, d.h. erster Schritt zum anlegen einer neuen Seite oder eines neuen Projektes ist immer das Erstellen eines neuen Buckets. Hierbei gibt es bereits zwei Punkte zu beachten:



  1. **Bucket Name**: Der Name kann weitestgehend frei gewählt werden, er muss allerdings S3-weit einzigartig sein, da Buckets immer auch über den Bucketname aufrufbar sind. Soll der Bucket später eine Website hosten (also z.B. _example.org_ oder _www.example.org_), dann sollte man den Bucket genau wie die Domain nennen, also in diesem Beispiel example.org, oder www.example.org sonst klappt die DNS-Auflösung später nicht.


  2. **Region**: Hier sollte man den Server-Standort wählen, der der potentiellen Zielgruppe am nächsten liegt, d.h. richtet sich die Website tendenziell eher an deutsche oder europäische Besucher, wählt man hier Irland als Standort.


Nach dem der Bucket erstellt wurde, müssen die Berechtigungen angepasst werden. In der default-Konfiguration ist der Bucket nur für einen selber freigegeben, soll er aber zum hosten einer Website dienen, muss er für alle Zugriffe freigegeben werden. Am einfachsten lässt sich das durch eine _Bucket-Policy_ umsetzen. Um eine neue Policy anzulegen, wählt man den Bucket aus, öffnet die _Properties_ und klickt dort auf _Permissions_. Über _Edit Bucket Policy_ startet man den _Bucket Policy Editor_ und trägt folgendes ein (_<bucketname>_ muss natürlich entsprechend ersetzt werden):

	{% highlight text %}
    {
    	"Version": "2008-10-17",
    	"Statement": [
    		{
    			"Sid": "AddPerm",
    			"Effect": "Allow",
    			"Principal": {
    				"AWS": "*"
    			},
    			"Action": "s3:GetObject",
    			"Resource": "arn:aws:s3:::<bucketname>/*"
    		}
    	]
    }
{% endhighlight %}

Nach dem der Bucket jetzt freigegeben ist, muss er noch für statisches Hosting aktiviert werden. Dies geschieht ebenfalls über die Einstellungen, über den Punkt _Static Website Hosting_. Über _Enable website hosting_ aktiviert man das hosting, man muss nun nur noch die Startseite festlegen, meistens wird das _index.html_ sein. Bei Bedarf kann auch ein Error-Document festgelegt werden. Bevor man die Einstellungen speichert, sollte man sich noch den _Endpoint_ kopieren, dies ist die Adresse, über die der Bucket(-Inhalt) erreichbar ist. Hat man Irland als Standort gewählt, sieht die Adresse wie folgt aus:

{% highlight text %}
    bucketname.s3-website-eu-west-1.amazonaws.com
{% endhighlight %}

Soll die Seite über einen eigenen Domain-Namen erreichbar sein, legt man einfach bei seinem Domain-Registrar bzw. seinem DNS-Anbieter einen _CNAME-Record_ mit diese Ziel an. An dieser Stelle sei noch einmal darauf hingewiesen: Der Bucketname muss genauso heißen wie die (Sub-)Domain, sonst klappt es nicht. Außerdem funktioniert das nicht für second-level Domains, also z.B. _example.org_, sondern nur für Sub-Domains, also z.B. _www.example.org_. Um einen S3 Bucket über eine "Root-Domain" erreichbar zu machen, muss man die gesamte Namensauflösung dieser Domain an Amazon Route 53 übertragen (mehr dazu in einem späteren Post).

Nachdem alle Vorbereitungen erledigt wurden, kann die Seite nun in den Bucket kopiert werden. Dafür gibt es viele Möglichkeiten, z.B. kann man Dateien auch direkt über die AWS Konsole kopieren. Bei einzelnen Datei ist das sicher die einfachste Möglichkeit, möchte man jedoch ganze Webseiten kopieren, ist es besser ein anderes Tool zu benutzen. Es gibt für jedes Betriebssystem verschiedene Lösungen, Browser-Plugins, etc., eine Google-Suche hilft da weiter. Für Mac-User kann ich [Cyberduck](http://cyberduck.ch/) empfehlen, da man mit Cyberduck auch einfach die Metadaten von S3-Objekten bearbeiten kann, was später bei der Performance-Optimierung wichtig wird.

Um den S3-Zugriff über ein Programm zu ermöglichen, benötigt man Zugangsdaten. Bei umfangreicheren AWS Konten mit mehreren Benutzern und/oder Buckets sollte man über die AWS Konsole _IAM_ konfigurieren, für einen schnellen Test tun es aber auch die Root-Zugangsdaten. (Man sollte sich allerdings bewusst sein, dass mit diesen Zugangsdaten praktisch alles in AWS möglich ist und ein entsprechendes Risiko besteht!) Um den AWS Rootkey abzufrufen klickt man in der AWS Konsole oben auf seinen Namen und dann auf _Security Credentials_. Dort kann man dann die _AWSAccessKeyId_ und den _AWSSecretKey_ abrufen.

Mit den Zugangsdaten kann man dann einfach den S3-Zugriff in dem Programm seiner Wahl einrichten und die gesamte Website in den Bucket kopieren. Nun ist die Seite online und über den oben genannten Link sowie alle erstellten CNAMEs zu erreichen. Beim testen wird man jedoch schnell bemerken, dass es hinsichtlich Performance Optimierung noch einiges zu tun gibt. So wird z.B. [Google Page Speed](http://developers.google.com/speed/pagespeed/insights/) anmerken, dass die Seite nicht komprimiert übertragen wird und das kein Browser-Caching für statische Inhalte definiert ist. Das Gute zuerst: Diese Probleme lassen sich lösen. Das Schlechte: Es ist relativ aufwendig, daher mehr dazu in einem weiteren Post. Eine weitere Möglichkeit, die Website zu beschleunigen, bietet Amazon CloudFront in Form eines CDN (ContentDeliveryNetwork). Wie man Amazon CloudFront konfiguriert, ist Thema des [nächsten Posts](http://blog.irhh.de/2013/statische-webinhalte-durch-amazon-cloudfront-bereitstellen/).
