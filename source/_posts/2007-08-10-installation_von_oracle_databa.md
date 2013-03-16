---
title: Installation von Oracle Database XE auf Ubuntu Linux
author: sw
layout: post
permalink: /2007/08/installation_von_oracle_databa/
categories:
  - Uncategorized
tags:
  - anleitung
  - apt
  - database
  - gpg
  - howto
  - installation
  - linux
  - net listener
  - oracle
  - swap
  - ubuntu
---
# 

Die Installation von Oracle® Database Express Edition 10g Release 2 (10.2) for Linux erfolgt auf Ubuntu Linux (feisty) wie in der [Anleitung des Herstellers][1] beschrieben.  
Nicht in der Anleitung erwähnt wird, dass es auch ein Debian repository für Oracle XE gibt, das sich mit folgendem Eintrag in der Datei /etc/apt/sources.list nutzen lässt.

 [1]: http://www.oracle.com/technology/software/products/database/xe/files/install.102/b25144/toc.htm

    deb http://oss.oracle.com/debian unstable main non-free

Der für Secure-APT benötigte [Public Key (2E2BCDBCB38A8516)][2] wird geladen, geprüft und installiert.

 [2]: http://oss.oracle.com/el4/RPM-GPG-KEY-oracle

    # wget -q http://oss.oracle.com/el4/RPM-GPG-KEY-oracle
    # gpg –import RPM-GPG-KEY-oracle
    gpg: key B38A8516: public key “Oracle OSS group (Open Source Software group) ” imported
    gpg: Total number processed: 1
    gpg:               imported: 1
    # gpg –fingerprint build@oss.oracle.com
    pub   1024D/B38A8516 2006-09-05 [expires: 2011-09-04]
          Key fingerprint = 1122 A29A B257 825F 322C 234E 2E2B CDBC B38A 8516
    uid                  Oracle OSS group (Open Source Software group) 
    sub   2048g/0042D4F4 2006-09-05 [expires: 2011-09-04]
    # gpg –armor –export B38A8516 | apt-key add -
    OK

Nun kann das Paket installiert werden.

    # aptitude install oracle-xe-universal

Die Oracle Datenbank erwartet bei 512 Megabyte oder mehr RAM mindestens ein Gigabyte Swap space. Der erforderliche Speicher stand auf dem System nicht zur Verfügung und so wurde der [Swap durch eine Datei erweitert][3]. Offenbar findet die Überprüfung der Größe des Swap-Speichers nur bei der Installation und nicht beim Betrieb der Datenbank statt. Somit wird das Swap-File nur für die Installation benötigt.  
Ist die Installation abgeschlossen, muss die Datenbank mit folgendem Befehl konfiguriert werden.

 [3]: http://syslog.warten.de/2007/08/swap_erweitern_durch_dateien.html

    # /etc/init.d/oracle-xe configure

Weiterhin sind die Umgebungsvariablen für Oracle-Benutzer zu setzen. Bei dieser Installation soll allein der Benutzer oracle die Datenbank benutzen. Hierzu wird in das `.bash_profile` des Benutzers ein Skript eingebunden, dass alle benötigten enviroment variables setzt.

    # echo ". /usr/lib/oracle/xe/app/oracle/product/10.2.0/server/bin/oracle_env.sh" >> /usr/lib/oracle/xe/.bash_profile

Die Installation lief scheinbar erfolgreich durch und es wurde der fehlerfreie Start des Oracle Net Listener und der Oracle Database 10g Express Edition Instance gemeldet. Tatsächlich brachen alle Oracle-Prozesse sofort ab, weil der Net Listener keine Verbindung zum target host aufbauen konnte.  
Die Ursache war in der Datei `/etc/hosts` zu finden. Da der Ursprung des Fehlers in der Datei unklar ist, sei der Hinweis auf sie an dieser Stelle ausreichend.  
Ist die Oracle-Datenbank erfolgreich installiert und konfiguriert worden, kann es losgehen. Da der Benutzer oracle Mitglied der Gruppe dba ist, muss kein Passwort eingegeben werden.

    # su – oracle
    $ sqlplus sys as sysdba
    SQL*Plus: Release 10.2.0.1.0 – Production on Fri Aug 10 22:04:28 2007
    Copyright (c) 1982, 2005, Oracle. All rights reserved.
    Enter password:
    Connected to:
    Oracle Database 10g Express Edition Release 10.2.0.1.0 – Production
    SQL> 
