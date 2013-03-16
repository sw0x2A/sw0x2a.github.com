---
title: Debian Linux mit debootstrap auf Host Europe VPS installieren
author: sw
layout: post
permalink: /2007/12/debian_linux_mit_debootstrap_a/
categories:
  - Uncategorized
tags:
  - anleitung
  - debian
  - host europe
  - howto
  - linux
  - v-server
  - virtuozzo
  - vps
  - vserver
---
# 

**[EDIT: December 8, 2007 9:45 PM: Das nachfolgend beschriebene Vorgehen führt zu Fehlern beim Restore eines über das Virtuozzo Control Panel erstellten Backups. Es ist daher nicht zu empfehlen, wenn die Backup-Funktionalität des Control Panels genutzt werden soll. Weitere Informationen zur Beeinträchtigung finden Sie in diesem [Artikel][1].]**

 [1]: http://syslog.warten.de/2007/12/restore-failed-auf-vps-linux.html

Das Debian-Image der [VPS Linux bei Host Europe][2] hat mehr Pakete vorinstalliert als für manche Zwecke notwendig sind. Diese könnten natürlich einfach entfernt werden. In diesem Fall soll jedoch ein Debian Linux mit debootstrap auf dem virtuellen Server komplett neu installiert werden. Das Vorgehen orientiert sich weitgehend an der [Installationsanleitung][3] der Debian-Entwickler. Durch die Virtualisierungstechnik sind jedoch einige Besonderheiten zu beachten.

 [2]: http://www.hosteurope.de/produkte/VPS-Linux
 [3]: http://www.debian.org/releases/stable/hppa/apds03.html.de

Zu Beginn ist der Server über das Virtuozzo Power Panel im Menüpunkt VPS Management > Maintenance in den Repair Mode zu versetzen. Das Virtuozzo Power Panel ist über https://VPS-IP:4643/ aufzurufen. Im Repair Mode ist das Dateisystem der VPS nach /repair gemountet.

Das Dateisystem der VPS wird komplett gelöscht, danach debootstrap installiert und ausgeführt.

    # rm -rf /repair/*  
    # mkdir /tmp/work  
    # cd /tmp/work/  
    # wget http://ftp.hosteurope.de/mirror/ftp.debian.org/debian/pool/main/d/debootstrap/debootstrap\_1.0.7\_all.deb  
    # ar -xf debootstrap\_1.0.7\_all.deb  
    # cd /  
    # zcat /tmp/work/data.tar.gz | tar xv  
    # /usr/sbin/debootstrap –arch i386 etch /repair http://ftp.hosteurope.de/mirror/ftp.debian.org/debian/ 

Nachdem das Basissystem installiert ist, müssen ein paar Anpassungen vorgenommen werden. In der Regel können die Konfigurationsdateien aus dem Repair Mode mit `cp -a /etc/filename /repair/etc/filename` übernommen werden. Es werden die Pseudo-Dateisysteme in fstab hinzugefügt, …

    # vi /repair/etc/fstab 

    proc  /proc       proc    defaults    0    0  
    none  /dev/pts    devpts  rw          0    0 

… ein Template für die Netzwerkkonfiguration hinterlegt, …

    # vi /repair/etc/network/interfaces.template 
    
    auto lo  
    iface lo inet loopback  
           address 127.0.0.1  
           netmask 255.0.0.0  
           broadcast 127.255.255.255  
           up ip route replace 127.0.0.0/8 dev lo 

… die sources.list erweitert, …

    # vi /repair/etc/apt/sources.list 

    #Host Europe server:  
    deb http://ftp.hosteurope.de/mirror/ftp.debian.org/debian etch main  
    deb http://ftp.hosteurope.de/mirror/ftp.debian.org/debian-security etch/updates main 
    deb http://security.debian.org etch/updates main  
    deb-src http://security.debian.org/ etch/updates main

    #deb http://ftp.de.debian.org/debian/ etch main contrib non-free  
    #deb-src http://ftp.de.debian.org/debian etch main contrib non-free  
    … die hosts-Datei angepasst und …

    # vi /repair/etc/hosts 

    127.0.0.1     localhost localhost.localdomain  
    10.20.30.40   example.com 

… in der inittab werden die Konsolen einkommentiert, da sie auf dem VPS nicht nutzbar sind.

    # vi /repair/etc/inittab   
    [...]  
    # Note that on most Debian systems tty7 is used by the X Window System,  
    # so if you want to add more getty’s go ahead but skip tty7 if you run X.  
    #  
    #1:2345:respawn:/sbin/getty 38400 tty1  
    #2:23:respawn:/sbin/getty 38400 tty2  
    #3:23:respawn:/sbin/getty 38400 tty3  
    #4:23:respawn:/sbin/getty 38400 tty4  
    #5:23:respawn:/sbin/getty 38400 tty5  
    #6:23:respawn:/sbin/getty 38400 tty6  
    [...] 

Es wird eine chroot-Umgebung aufgebaut, damit zusätzliche Pakete installiert und das neue System bearbeitet und konfiguriert werden können.

    # LANG=C chroot /repair /bin/bash 

Zuerst wird die Zeitzone eingestellt.

    # tzconfig 

Dann muss der sshd installiert werden. Außerdem werden locales und console-data installiert, um Sprach- und Lokalisierungsanpassungen vornehmen zu können. Um beispielsweise ein englischsprachiges System mit einer deutschen Tastaturbelegung zu nutzen, wird bei den locales “en\_US.UTF-8″ und in console\_data die Keymap “pc / qwertz / German / Standard / latin1″ ausgewählt.

    # aptitude update  
    # aptitude install openssh-server  
    # aptitude install locales  
    # dpkg-reconfigure locales  
    # aptitude install console-data  
    # dpkg-reconfigure console-data 

Da bei jedem Neustart des V-Servers der Hostname zurückgesetzt wird, muss er beim Booten neu geschrieben werden. Es wird hierfür die in der [Host Europe FAQ][4] vorgeschlagene Lösung eingesetzt.

    # vi /etc/init.d/hostname_vps 

    #!/bin/bash  
    HOSTNAME=example.com  
    echo $HOSTNAME > /etc/hostname  
    /bin/hostname $HOSTNAME  
    if [ -f /etc/my.resolv.conf ]; then cp /etc/my.resolv.conf /etc/resolv.conf ; fi  
    exit  

Das Init-Skript wird erstellt, mit Ausführrechten versehen und verlinkt, damit es bei jedem Bootvorgang ausgeführt wird.

    # chmod x /etc/init.d/hostname_vps  
    # update-rc.d hostname_vps defaults 09 

Zum Abschluß wird das root-Passwort gesetzt, die chroot-Umgebung verlassen und ausgeloggt.

    # /etc/init.d/ssh stop  
    # passwd  
    # exit  
    # logout 

Die Installation von Debian Linux ist nun vollständig abgeschlossen und der Server muss rebootet werden. Hierzu wird der Repair Mode im Virtuozzo Power Panel beendet. Nach ein paar Sekunden sollte man sich auf dem neugestarteten Server anmelden können.

 [4]: http://www.hosteurope.de/faq/index.php?cpid=11517
