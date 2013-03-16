---
title: Swap erweitern durch Dateien
author: sw
layout: post
permalink: /2007/08/swap_erweitern_durch_dateien/
categories:
  - Uncategorized
tags:
  - fstab
  - linux
  - memory
  - swap
---
# 

Wenn die als Swap zur Verfügung stehende Festplattenpartition zu klein ist, kann der Swap-Speicher durch Dateien im Linux-Filesystem erweitert werden. Dieser Weg sollte selbstverständlich nur eine Ausnahme darstellen, da ein Swap-Speicher in einer Datei noch langsamer als ohnehin schon ist.

Um eine Swap-Datei hinzuzufügen ist wie folgt vorzugehen:

Zuerst wird eine Datei der gewünschten zusätzlichen Swap-Größe erstellt (hier 1GB).

    # dd if=/dev/zero of=/swap-file bs=1024 count=1048576 

Nun wird ein Swap-Bereich in der Datei erstellt…

    # mkswap /swap-file 

… und aktiviert.

    # swapon /swap-file 

Damit das neue Swap-File beim Booten aktiviert wird, muss folgender Eintrag der Datei `/etc/fstab` hinzugefügt werden:

    /swap-file swap swap defaults 0 0
