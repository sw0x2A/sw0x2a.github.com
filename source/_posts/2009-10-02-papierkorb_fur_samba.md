---
title: Papierkorb für Samba
author: sw
layout: post
permalink: /2009/10/papierkorb_fur_samba/
categories:
  - Uncategorized
tags:
  - linux
  - recycle
  - samba
  - windows
---
# 

Der Gedanke, dass Dateien, die auf dem Fileserver liegen, von den Benutzern versehentlich gelöscht werden könnten und diese dann nur aus den Backups wiederherzugestellen seien, ließ dem Vorgesetzten zwanzig graue Haare wachsen und mich nach einer Lösung suchen. Die besteht aus dem [VFS-Modul recycle][1] für Samba, dass die Papierkorb-Funktion von Windows nachahmt. Wird es benutzt, werden UNLINK-Befehle abgefangen, und die Datei in den recycle-Ordner verschoben, anstatt gelöscht zu werden.

 [1]: http://www.samba.org/samba/docs/man/Samba-HOWTO-Collection/VFS.html#id2651366

Bei Verfügbarkeit des Moduls sollte im Bibliotheksverzeichnis von Samba unter vfs die Datei recycle.so zu finden sein, üblicherweise `/usr/lib/samba/vfs/recycle.so` auf Linux oder `/usr/local/lib/samba/vfs/recycle.so` auf BSD.

Am Beispiel einer für alle Benutzer zugänglichen Freigabe sei die Konfiguration gezeigt. Mit der Definition von `vfs object = recycle` wird das Modul geladen und danach weitere Parameter übergeben. Eine Erklärung zu den Optionen ist der Dokumentation zu entnehmen.

    [public]
    path = /var/fileserver/public
    writeable = Yes
    browseable = Yes
    create mask = 0666
    directory mask = 0777
    vfs object = recycle
    recycle:repository = .recycle
    recycle:keeptree = Yes
    recycle:touch = Yes
    recycle:versions = Yes
    recycle:maxsize = 0
    recycle:directory_mode = 0777

Das Verzeichnis `.recycle` wird automatisch in oberster Ebene der Freigabe angelegt und eine gelöschte Dateien und Verzeichnisse nun dort hin verschoben.
