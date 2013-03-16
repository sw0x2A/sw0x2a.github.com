---
title: Passwort von Windows-Benutzerkonto zurücksetzen
author: sw
layout: post
permalink: /2009/07/passwort_von_windows-benutzerk/
categories:
  - Uncategorized
tags:
  - anleitung
  - chntpw
  - grml
  - howto
  - linux
  - password recovery
  - passwort
  - windows
---
# 

Wenn das Zugangspasswort für ein Benutzerkonto im Windows unbekannt ist und zurückgesetzt werden soll, kann auch der Linux-Admin schnell Abhilfe leisten. Hierzu bedarf es lediglich eines Bootmediums mit Linux und dem Tool [chntpw][1]. In meinem Fall ist das ein USB-Stick mit [GRML][2], das chntpw bereits mitbringt.

 [1]: http://freshmeat.net/projects/chntpw/
 [2]: http://grml.org/

Ist das System gestartet, wird die Windows-Partition gemountet und in das Verzeichnis `WINDOWSSystem32config` gewechselt. Dort liegt die Datei SAM, die die Accountdaten, also auch die Passwort-Hashes, beinhaltet.

    # mkdir /mnt/windows
    # ntfs-3g /dev/sda1 /mnt/windows
    # cd /mnt/windows/WINDOWS/system32/config

Mit dem folgenden Kommando kann man sich die einzelnen Konten anzeigen lassen…

    # chntpw -l SAM

…und dann zur Tat schreiten und das Passwort löschen oder neu setzen:

    # chntpw -u username SAM

Im letzten Kommando ist ‘username’ dabei selbstverständlich durch den Benutzernamen des Windows-Kontos zu ersetzen.
