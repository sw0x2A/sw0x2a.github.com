---
title: Smarthost signiert Mails mit DKIM
author: sw
layout: post
permalink: /2009/02/smarthost_signiert_mails_mit_d/
categories:
  - Uncategorized
tags:
  - anti-spam
  - dkim
  - domainkeys
  - mail
  - milter
  - postfix
  - smarthost
  - smtp
---
# 

Die Produktionsserver relayen alle Mails an einen zentralen Mailserver, der dann den Versand übernimmt. Zusätzlich soll der Smarthost nun alle Mails mit DKIM signieren. Ich habe mich für den [dkim-milter][1] entschieden und die Umsetzung ging dank seiner Helferlein wie dkim-genkey recht schnell und einfach.

 [1]: http://sourceforge.net/projects/dkim-milter/

Damit der dkim-filter für mehrere Domains signierend tätig werden kann, muss man die Schlüsselinformationen in einer Datei hinterlegen und deren Pfad mit KeyList übergeben. Die Paramater Domain, KeyFile und Selector können ausschliesslich für Ein-Domain-Lösungen verwendet werden.

Die an KeyList übergebene Datei könnte beispielsweise wie folgt aussehen. Durch Doppelpunkte getrennt werden die für die Signierung zugelassenen E-Mail-Adressen, der Domainname (die Redundanz an dieser Stelle ist auffällig häßlich und historisch bedingt) sowie der Pfad zum keyfile angebenen. Die Angabe der Endungen .private und .pem ist optional.

    relay:~# cat /etc/dkim-keys.conf
    *@example.de:example.de:/etc/dkim/example.de/2009
    *@example.com:example.com:/etc/dkim/example.com/2009
    *@example.es:example.es:/etc/dkim/example.es/2009
    *@example.fr:example.fr:/etc/dkim/example.fr/2009
    *@example.nl:example.nl:/etc/dkim/example.nl/2009
    *@example.pl:example.pl:/etc/dkim/example.pl/2009
    *@example.se:example.se:/etc/dkim/example.se/2009

Nach den notwendigen DNS-Änderungen (TXT für z.B. 2009._domainkey.example.com) funktionierte der lokale Versand wie gewünscht und mit gültiger Signatur. Allein die (wichtigen) externen Hosts wurden ignoriert. Im Logfile sieht das dann so aus:

    (unknown-jobid) external host www.example.com attempted to send as example.de

Ein Blick in die man-Page konnte auch dieses Problem lösen: Die Grundeinstellung sieht nur eine Signierung von Mails von 127.0.0.1 vor. Mit dem Aufrufparameter `-i` kann man den Pfad einer Datei übergeben, die pro Zeile eine IP-Adresse weiterer zustellender Systeme enthält.

    relay:~# cat /etc/default/dkim-filter
    # Command-line options specified here will override the contents of
    # /etc/dkim-filter.conf. See dkim-filter(8) for a complete list of options.
    DAEMON_OPTS="-i /etc/dkim-peers.conf"
    [...]
    relay:~# cat /etc/dkim-peers.conf
    127.0.0.1
    10.17.133.217
