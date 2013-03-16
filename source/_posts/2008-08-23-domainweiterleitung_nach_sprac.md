---
title: Domain-Weiterleitung nach Spracheinstellung im Webbrowser
author: sw
layout: post
permalink: /2008/08/domainweiterleitung_nach_sprac/
categories:
  - Uncategorized
tags:
  - apache
  - http
  - internationalisierung
  - rewrite
  - webserver
---
# 

Es soll beim Aufruf von http://example.com/ basierend auf den Webbrowser-Einstellungen die Homepage des jeweiligen Landes aufgerufen werden. Der Browser übermittelt die bevorzugte Anzeigesprache im HTTP request im header field `Accept-Language`. Das [Modul rewrite][1] des Apache kann den HTTP header auswerten, also kann die Anforderung direkt in der Konfiguration des Webservers umgesetzt werden.

 [1]: http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html

    RewriteEngine on
    RewriteCond %{HTTP:Accept-Language} ^de.* [NC]
    RewriteRule (.*) http://de.example.com/$1 [L,R=permanent]
    

Sendet der Browser beim Aufruf des vhosts den Wunsch mit, Seiten in deutscher Sprache (de) zu erhalten, wird er in obigem Beispiel mittels HTTP response 301 (permanent redirect) auf http://de.example.com/ weitergeleitet. Ein eventuell angegebener Pfad in der URL bleibt erhalten.