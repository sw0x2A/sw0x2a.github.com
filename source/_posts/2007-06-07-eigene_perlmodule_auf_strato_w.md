---
title: Eigene Perl-Module auf STRATO Webhosting installieren
author: sw
layout: post
permalink: /2007/06/eigene_perlmodule_auf_strato_w/
categories:
  - Uncategorized
tags:
  - cgi
  - cpan
  - module
  - perl
  - strato
---
# 

Wenn man eigene Perl-Module in STRATO Webhosting-Paketen nutzen möchte, kann man diese selbst installieren.  
Das Array `@INC` enthält die Verzeichnisse, in denen Perl nach Modulen sucht, die über Skripte nachgeladen werden sollen. Das Array enthält auch das Verzeichnis ‘.’, das das aktuelle Verzeichnis repräsentiert.

Dies ermöglicht es dem Benutzer, ausgehend vom Skriptpfad, Module nachzuinstallieren. Die folgende Anleitung beschreibt anhand des CPAN-Moduls CGI::Session die Installation eigener Module.  
Wenn man auf der Suche nach einer bestimmten Funktion ist, kann diese vielleicht bei [CPAN][1], einer Suchmaschine für Perl-Module gefunden werden.  
Holen und entpacken Sie das Modul [CGI::Session][2].

 [1]: http://search.cpan.org/
 [2]: http://search.cpan.org/author/MARKSTOS/CGI-Session-4.20/lib/CGI/Session.pm

Üblicherweise befindet sich im Archiv ein Verzeichnis `/lib`, das Unterverzeichnisse mit den Moduldateien enthält. In diesem Fall liegt in `/lib` das Verzeichnis `CGI`.  
Der Pfad zum Perl-Modul ergibt sich aus seinem Namen: Das Modul CGI::Session muss im Verzeichnis `./CGI/Session.pm` liegen. (Ein Modul Test::Example::Hello müsste unter `./Test/Example/Hello.pm` abgelegt werden.) Es ist auf jeden Fall die Großkleinschreiben zu beachten.

Kopieren Sie diese Verzeichnisse in das Verzeichnis `/cgi-bin` auf Ihrem Webspace. Das Modul ist jetzt installiert und kann nun aus Ihren Skripten heraus aufgerufen werden. Erstellen Sie ein kleines Testskript mit folgendem Inhalt:

    #!/usr/bin/perl58  
    use CGI::Session;  
    $session = new CGI::Session();  
    $CGISESSID = $session->id();  
    [print][3] $session->header();

Wenn Sie das Skript auf der Shell ausführen, werden Sie sehen, dass ein Cookie erzeugt wurde:

 [3]: http://perldoc.perl.org/functions/print.html

    > ./session.cgi  
    Set-Cookie: CGISESSID=7d070ddee98d8287ed1827b9e37391c0; path=/  
    Date: Tue, 05 Jun 2007 13:08:57 GMT  
    Content-Type: text/html; charset=ISO-8859-1 

Das Modul wurde erfolgreich installiert und kann benutzt werden.
