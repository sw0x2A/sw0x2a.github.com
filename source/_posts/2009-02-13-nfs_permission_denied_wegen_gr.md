---
title: 'NFS: Permission denied wegen Group'
author: sw
layout: post
permalink: /2009/02/nfs_permission_denied_wegen_gr/
categories:
  - Uncategorized
tags:
  - group
  - nfs
  - permission
  - rpc
---
# 

RPC, das von NFS für die Identifikation von Benutzern verwendet wird, kann nicht mehr als 16 Gruppen, deren Mitglied ein Benutzer ist, übermitteln. Hier hatten wir den Fall, dass eine Benutzerin plötzlich nicht mehr auf Verzeichnisse zugreifen konnte, für die sie aufgrund der Gruppenrechte Zugriff haben sollte (Permission denied). Selbstverständlich waren die GID auf NFS-Server und Client-System identisch. Sie war kürzlich einer 17. Gruppe zugeordnet worden und die für den Zugriff relevante Gruppe (sie hatte die höchste Group-ID) wurde nicht mehr an den NFS-Server übertragen.

Das war extrem schwer zu debuggen. Die Ursache wurde erst nach Stunden gefunden. Der Sachverhalt wird [hier][1] ausführlich erläutert. Die Lösung war einfach: Es wurde die Option –manage-gids beim Start von rpc.mountd hinzugefügt.

 [1]: http://nfsworld.blogspot.com/2005/03/whats-deal-on-16-group-id-limitation.html