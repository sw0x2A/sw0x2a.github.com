---
title: 'Bug: Network-Manager speichert PPP-Einstellungen nicht'
author: sw
layout: post
permalink: /2008/12/bug_network-manager_speichert/
categories:
  - Uncategorized
tags:
  - bugs
  - linux
  - mobile broadband
  - network-manager
  - ppp
  - ubuntu
  - umts
---
# 

Die aktuelle Version des network-manager (network-manager-gnome: 0.7~~svn20081020t000444-0ubuntu1) in Ubuntu 8.10 [speichert nicht die PPP-Einstellungen für Mobile Broadband-Verbindungen][1]. Welche Änderungen auch gemacht werden, die Software schreibt undefinierte Werte nach gconf.

 [1]: https://bugs.launchpad.net/bugs/306303

Mit dem gconf-editor kann man die Werte manuell setzen. In /system/networking/connections/NUMMER/ppp) werden bei Änderungen im network-manager die keys mit undefinierten Werten geschrieben, die man dann selbst setzen muss.