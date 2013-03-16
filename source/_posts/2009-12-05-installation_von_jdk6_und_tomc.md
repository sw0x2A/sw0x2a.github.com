---
title: Installation von JDK6 und Tomcat6 auf RHEL
author: sw
layout: post
permalink: /2009/12/installation_von_jdk6_und_tomc/
categories:
  - Uncategorized
tags:
  - centos
  - java
  - jpackage
  - linux
  - red hat
  - rhel
  - rpm
  - rpmbuild
  - sun
  - tomcat
---
# 

Im folgenden Text soll die Installation von Sun JDK 6 und Tomcat6 auf einem System mit RHEL5 oder Centos5 beschrieben werden. Da die Pakete nicht in den Repositories der Distribution verfügbar sind, müssen sie von extern bezogen bzw. selbst gebaut werden.

**JPackage Repository nutzen**  
Das [JPackage Projekt][1] bietet eine umfangreiche Sammlung von Java-Software-Paketen für Linux an, die wir nun nutzen möchten. Zuerst wird das neue [Repository eingebunden][2] und der [GPG-Schlüssel importiert][3].

 [1]: http://www.jpackage.org/
 [2]: http://www.jpackage.org/yum.php
 [3]: http://www.jpackage.org/gpgkey.php

    wget -O /etc/yum.repos.d/jpackage50.repo http://www.jpackage.org/jpackage50.repo
    rpm --import http://www.jpackage.org/jpackage.asc

**Sun JDK 6 installieren**  
Aus lizenzrechtlichen Gründen kann kein fertiges RPM-Paket für das Java6 JDK angeboten werden. Es gibt aber ein Source-Paket, mit dessen Hilfe aus dem auf der Java-Seite erhältlichen Software ein ordentliches RPM gebaut werden kann. Sofern nicht schon vorhanden, müssen hierfür erforderliche Tools installiert werden. Das Sun JDK6 muß separat [heruntergeladen][4] werden. Sollte für die aktuelle Version noch kein Paket verfügbar sein, findet man im Archiv die passende [ältere Version][5].

 [4]: http://java.sun.com/javase/downloads/
 [5]: http://java.sun.com/products/archive/

    yum install rpm-build jpackage-utils libXtst
    wget -O /tmp/java-1.6.0-sun-1.6.0.14-1jpp.nosrc.rpm http://mirrors.dotsrc.org/jpackage/6.0/generic/non-free/SRPMS/java-1.6.0-sun-1.6.0.14-1jpp.nosrc.rpm
    rpm -ihv /tmp/java-1.6.0-sun-1.6.0.14-1jpp.nosrc.rpm
    mv /tmp/jdk-6u14-linux-x64.bin /usr/src/redhat/SOURCES/
    rpmbuild -bb /usr/src/redhat/SPECS/java-1.6.0-sun.spec
    rpm -ihv /usr/src/redhat/RPMS/x86_64/java-1.6.0-sun-1.6.0.14-1jpp.x86_64.rpm
    rpm -ihv /usr/src/redhat/RPMS/x86_64/java-1.6.0-sun-devel-1.6.0.14-1jpp.x86_64.rpm

**Tomcat 6 installieren**  
Tomcat6 sollte sich nun einfach über die Paketverwaltung installieren lassen. Leider gibt es einen Abhängigkeitenkonflikt zwischen benötigten Paketen.

    Error: Missing Dependency: /usr/bin/rebuild-security-providers is needed by package java-1.4.2-gcj-compat-1.4.2.0-40jpp.115.x86_64 (base)

Eine Lösung ist der Bau und die Installation eines Kompatibilitätspakets wie es in einem Posting auf der Mailinglist [JPackage-discuss][6] beschrieben ist. Danach klappt auch die Installation von Tomcat6 ohne Probleme.

 [6]: http://zarb.org/pipermail/jpackage-discuss/2008-July/012751.html

    rpmbuild -bb /usr/src/redhat/SPECS/jpackage-utils-compat.spec
    rpm -ihv /usr/src/redhat/RPMS/noarch/jpackage-utils-compat-el5-0.0.1-1.noarch.rpm
    yum install tomcat6

Die Installation weiterer Java-Software aus dem JPackage-Softwareverzeichnis sollte nun einfach über yum möglich sein.
