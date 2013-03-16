---
title: FastCGI has failed to remain running for 30 seconds
author: sw
layout: post
permalink: /2007/03/fastcgi_has_failed_to_remain_r/
categories:
  - Uncategorized
tags:
  - apache
  - fastcgi
  - perl
---
# 

Nach der Installation von FastCGI und dem ersten Test mit einem CGI-Skript sind folgende Fehlermeldungen im error_log vom Apache zu finden:

    [error] [client 127.0.0.1] FastCGI: incomplete headers (0 bytes) received from server "/var/www/localhost/cgi-bin/hello.fcgi"

und mehrfach

    [warn] FastCGI: (dynamic) server "/var/www/localhost/cgi-bin/hello.fcgi" has failed to remain running for 30 seconds given 3 attempts, its restart interval has been backed off to 600 seconds

Mögliche Ursachen sind der vorangegangene Versuch ein gewöhnliches CGI-Skript mit FastCGI auszuführen und die Tatsache, dass das Perl-Modul FCGI nicht (in einem `@INC`-Pfad) installiert ist.
