---
title: Bernsteins daemontools installieren
author: sw
layout: post
permalink: /2008/07/bernsteins_daemontools_install/
categories:
  - Uncategorized
tags:
  - anleitung
  - daemontools
  - errno
  - gcc
  - glibc
  - howto
  - linux
---
# 

Die [daemontools][1] (in Version 0.76) lassen sich auf Systemen mit der glibc 2.3.x oder später nicht mehr ohne Änderung compilieren. Das musste ich heute auf einem CentOS 5.2 feststellen. Der Installationsversuch wurde mit den folgenden Zeilen erfolglos beendet.

 [1]: http://cr.yp.to/daemontools.html

    /usr/bin/ld: errno: TLS definition in /lib64/libc.so.6 section .tbss mismatches non-TLS reference in envdir.o
    /lib64/libc.so.6: could not read symbols: Bad value
    collect2: ld returned 1 exit status
    make: *** [envdir] Error 1
    Copying commands into ./command...
    cp: cannot stat `compile/svscan': No such file or directory

Ursache für die Fehlermeldung ist die zur aktuellen glibc [inkompatible Deklaration von `errno`][2] in den daemontools. Glücklicherweise lässt sich diese Einschränkung leicht durch einen [Patch][3] lösen.

 [2]: http://www.thedjbway.org/errno.html
 [3]: ftp://moni.csi.hu/pub/glibc-2.3.1/daemontools-0.76.errno.patch

Der Weg zur erfolgreichen Installation sieht dann Schritt für Schritt etwa wie folgt aus:

    cd /tmp
    wget http://cr.yp.to/daemontools/daemontools-0.76.tar.gz
    wget ftp://moni.csi.hu/pub/glibc-2.3.1/daemontools-0.76.errno.patch
    mkdir -p /package
    chmod 1755 /package
    cd /package/
    tar xzpf /tmp/daemontools-0.76.tar
    cd admin/daemontools-0.76
    patch -p1 < /tmp/daemontools-0.76.errno.patch
    package/install

Wer nicht patchen möchte, kann den Compiler (hier gcc) explizit anweisen, /usr/include/errno.h zu inkludieren.

    # vi src/conf-cc
    gcc -include /usr/include/errno.h -O2 -Wimplicit -Wunused -Wcomment -Wchar-subscripts -Wuninitialized -Wshadow -Wcast-qual -Wcast-align -Wwrite-strings
