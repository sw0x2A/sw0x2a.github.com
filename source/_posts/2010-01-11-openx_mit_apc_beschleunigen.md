---
title: OpenX mit APC beschleunigen
author: sw
layout: post
permalink: /2010/01/openx_mit_apc_beschleunigen/
categories:
  - Uncategorized
tags:
  - ad server
  - apache
  - apc
  - cache
  - httpd
  - opcode
  - openx
  - performance
  - php
  - php accelerator
  - webserver
---
# 

Die Auslastung eines von mir betreuten Webserver, der hauptsächlich den Adserver OpenX ausführt, ließ mich nach Optimierungsmöglichkeiten suchen. Schnell wurde ich in [diesem Artikel][1] fündig. Dort wird dringend geraten einen PHP accelerator wie [APC (Alternative PHP Cache)][2] zu verwenden. Ein solcher Opcode-Cache speichert den kompilierten PHP-Quelltext zwischen und kann dadurch bei wiederholter Ausführung das zeitaufwändige Kompilieren nahezu vollständig vermeiden.

 [1]: http://www.openx.org/en/docs/whitepapers/performance-tuning
 [2]: http://pecl.php.net/package/APC

Die versprochene Beschleunigung klingt traumhaft und kann tatsächlich erreicht werden, wie folgende Graphen aus dem Monitoring des Servers zeigen. APC wurde an Tag 8 gestartet.

[![][4]][4]  
[![][5]][5]

 [4]: /assets/2010/01/cpu-week.png
 [5]: /assets/2010/01/load-week.png

Ähnliche Ergebnisse sollten sich mit anderen PHP-Anwendungen und einem Opcode-Cache ebenfalls erzielen lassen.
