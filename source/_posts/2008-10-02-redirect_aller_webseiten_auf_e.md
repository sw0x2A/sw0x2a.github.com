---
title: Redirect aller Webseiten auf eine Datei
author: sw
layout: post
permalink: /2008/10/redirect_aller_webseiten_auf_e/
categories:
  - Uncategorized
tags:
  - apache
  - html
  - php
  - redirect
  - rewrite
  - wartungsarbeiten
  - webdesign
---
# 

Wenn man beispielsweise wegen Wartungsarbeiten an der Infrastruktur den ordentlichen Betrieb einer Website nicht mehr aufrecht erhalten kann und für den Zeitraum der Arbeiten eine Seite anzeigen möchte, die den Besucher kurz über die Wartungsarbeiten informiert und vertröstet, kann das recht einfach mit rewrite-Regeln im Webserver lösen.

    
    RewriteEngine On
    RewriteCond %{REQUEST_URI} !^/offline.php$
    RewriteRule (^$|.(html|php)$) http://www.example.com/offline.php [L]
    
    

Das Beispiel leitet beim direkten Aufruf des Domainnamens sowie beim Aufruf von HTML- und PHP-Dateien auf eine bestimmte Seite weiter. Diese Seite wird zuvor als Ausnahme von der Regel ausgenommen. Da nur HTML- und PHP-Dateien weitergeleitet werden, bleiben die Links zu Images und CSS o.ä. erhalten.