---
title: Schlüssel für Repository in Secure APT importieren
author: sw
layout: post
permalink: /2007/07/schlussel_fur_repository_in_se/
categories:
  - Uncategorized
tags:
  - apt-key
  - archive
  - deb
  - debian
  - gpg
  - public key
  - repository
  - secure apt
  - sources.list
---
# 

Mit Debian 4.0 (Etch) hat Secure APT Einzug in die Paketverwaltung gefunden. Es soll sicherstellen, dass nur vom Herausgeber signierte Debian-Archive installiert werden. Die Repositories enthalten hierfür eine Datei “Release”, die neben Informationen über die Releases selbst auch Prüfsummen der Paketindizes beinhaltet. Die Datei “Release” wird anhand einer GnuPG-Signatur verifiziert.

Für die Hauptrepositories sind die öffentlichen Schlüssel (public keys) bereits hinterlegt. Möchte man weitere Installationsquellen hinzufügen, beklagt die Paketverwaltung, dass der zuständige Schlüssel fehlt.

    W: GPG error: http://dl.google.com stable Release: The following signatures couldn’t be verified because the public key is not available: NO_PUBKEY A040830F7FAC5991  
    W: You may want to run apt-get update to correct these problems 

Mit den folgenden drei Schritten lässt sich der fehlende Schlüssel importieren:

1.  Der gewünschte Schlüssel kann von Keyservern heruntergeladen werden. 

        # gpg –keyserver subkeys.pgp.net –recv-key A040830F7FAC5991  
        gpg: requesting key 7FAC5991 from hkp server subkeys.pgp.net  
        gpg: key 7FAC5991: public key “Google, Inc. Linux Package Signing Key ” imported  
        gpg: no ultimately trusted keys found  
        gpg: Total number processed: 1  
        gpg: imported: 1 

2.  Prüfen Sie den Fingerabdruck des neuen Schlüssels. 

        # gpg –fingerprint A040830F7FAC5991  
        pub 1024D/7FAC5991 2007-03-08  
            Key fingerprint = 4CCA 1EAF 950C EE4A B839 76DC A040 830F 7FAC 5991  
        uid Google, Inc. Linux Package Signing Key   
        sub 2048g/C07CB649 2007-03-08 

3.  Können Sie davon ausgehen, dass es sich um den passenden Schlüssel für das hinzugefügte Repository handelt, exportieren Sie ihn aus Ihrem Schlüsselbund in ASCII-Armor-Form und übergeben ihn an `apt-key`. 

        # gpg –armor –export 7FAC5991 | apt-key add -  
        OK
