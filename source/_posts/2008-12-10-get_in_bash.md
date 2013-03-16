---
title: GET / im Terminal
author: sw
layout: post
permalink: /2008/12/get_in_bash/
categories:
  - Uncategorized
tags:
  - bash
  - http
  - linux
  - lwp
  - perl
  - terminal
---
# 

Da staunte ich eben nicht schlecht als meine telnet-Session abbrach und das Kommando `GET /` im Terminal ausgeführt wurde. Als Ausgabe wurde ein HTML-formatiertes `ls -1a /` zurückgegeben.

    $ telnet example.com 80
    Trying 208.77.188.166...
    GET /
    telnet: Unable to connect to remote host: Connection timed out
    $ GET /
    <HTML>
    <HEAD>
    <TITLE>Directory /</TITLE>
    <BASE HREF="file:/">
    </HEAD>
    <BODY>
    <H1>Directory listing of /</H1>
    <UL>
    <LI><A HREF="./">./</A>
    <LI><A HREF="../">../</A>
    <LI><A HREF=".rnd">.rnd</A>
    <LI><A HREF="aquota.group">aquota.group</A>
    <LI><A HREF="aquota.user">aquota.user</A>
    <LI><A HREF="bin/">bin/</A>
    <LI><A HREF="boot/">boot/</A>
    <LI><A HREF="dev/">dev/</A>
    <LI><A HREF="etc/">etc/</A>
    <LI><A HREF="home/">home/</A>
    <LI><A HREF="initrd/">initrd/</A>
    <LI><A HREF="lib/">lib/</A>
    <LI><A HREF="media/">media/</A>
    <LI><A HREF="mnt/">mnt/</A>
    <LI><A HREF="opt/">opt/</A>
    <LI><A HREF="proc/">proc/</A>
    <LI><A HREF="root/">root/</A>
    <LI><A HREF="sbin/">sbin/</A>
    <LI><A HREF="srv/">srv/</A>
    <LI><A HREF="sys/">sys/</A>
    <LI><A HREF="tmp/">tmp/</A>
    <LI><A HREF="usr/">usr/</A>
    <LI><A HREF="var/">var/</A>
    </UL>
    </BODY>
    </HTML>

Des Rätsels Lösung war schnell gefunden und ernüchternd. GET ist ein symbolischer Link auf [lwp-request][1], das benutzt werden kann, um Anfragen an WWW-Server und das lokale Dateisystem zu senden.

 [1]: http://search.cpan.org/~gaas/libwww-perl-5.805/bin/lwp-request

    $ ls -l `which GET`
    lrwxrwxrwx 1 root root 11 2008-11-02 21:09 /usr/bin/GET -> lwp-request
