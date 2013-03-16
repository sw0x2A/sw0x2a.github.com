---
title: CGI-Skripte zum Prüfen der Grundfunktionalität
author: sw
layout: post
permalink: /2008/03/cgiskripte_zum_prufen_der_grun/
categories:
  - Uncategorized
tags:
  - apache
  - cgi
  - fastcgi
  - fcgi
  - mysql
  - perl
  - scripts
  - sendmail
  - upload
  - webserver
---
# 

Zur Prüfung der Grundfunktionen des Webservers in Verbindung mit dem Common Gateway Interface (CGI) habe ich [ein paar simple CGI-Skripte in Perl geschrieben][1]:

 [1]: /assets/2008/03/cgi-test.tar.gz

*   hello.cgi — Ausgabe von “Hello, world!”
*   hello.fcgi — “Hello, world!” mit FCGI (FastCGI)
*   hellooo.cgi — “Hello, world!” mit CGI-Modul (objekt-orientiert)
*   mysql.cgi — Mit MySQL-Datenbank verbinden
*   sendmail.cgi — E-Mail über sendmail versenden
*   upload.cgi — Dateiupload
