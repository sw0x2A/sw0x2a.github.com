---
title: Austausch defekter Festplatte bei Hetzner
author: sw
layout: post
permalink: /2009/04/austausch_defekter_festplatte/
categories:
  - Uncategorized
tags:
  - defekt
  - error
  - festplatte
  - harddisk
  - hardware
  - hdd
  - hoster
  - linux
  - mdadm
  - media
  - raid
  - support
---
# 

Auf einen Hilfssystem, das wir von Hetzner hosten lassen, stellte ich gestern einen Hardwaredefekt bei einer der beiden Festplatten des RAID-1-Verbunds fest. Das RAID war degraded und in der dmesg waren die typischen Ausgaben zu finden.

    ata2: EH complete
    SCSI device sdb: 976773168 512-byte hdwr sectors (500108 MB)
    sdb: Write Protect is off
    sdb: Mode Sense: 00 3a 00 00
    SCSI device sdb: drive cache: write back
    ata2.00: exception Emask 0x0 SAct 0x1 SErr 0x0 action 0x0
    ata2.00: (irq_stat 0x40000008)
    ata2.00: tag 0 cmd 0x60 Emask 0x9 stat 0x41 err 0x40 (media error)
    sd 1:0:0:0: SCSI error: return code = 0x08000002
    sdb: Current: sense key: Medium Error
    Additional sense: Unrecovered read error - auto reallocate failed
    end_request: I/O error, dev sdb, sector 976767110
    ata2: EH complete
    SCSI device sdb: 976773168 512-byte hdwr sectors (500108 MB)
    sdb: Write Protect is off
    sdb: Mode Sense: 00 3a 00 00
    SCSI device sdb: drive cache: write back
    ata2.00: WARNING: zero len r/w req
    ata2.00: WARNING: zero len r/w req
    ata2.00: WARNING: zero len r/w req
    ata2.00: WARNING: zero len r/w req> ata2.00: WARNING: zero len r/w req
    ata2.00: WARNING: zero len r/w req
    raid1: Disk failure on sdb2, disabling device.
    Operation continuing on 1 devices
    RAID1 conf printout:
    --- wd:1 rd:2
    disk 0, wo:0, o:1, dev:sda2
    disk 1, wo:1, o:0, dev:sdb2
    RAID1 conf printout:
    --- wd:1 rd:2
    disk 0, wo:0, o:1, dev:sda2

Kurz den Support bei Hetzner angeschrieben und den Sachverhalt geschildert, bekam ich nach zehn Minuten die erste Antwort und zwei weitere Mails (insgesamt etwa eine halbe Stunde) später hatte ich einen Termin am nächsten Morgen für den Austausch der Platte. Versprochene Downtime war etwa zehn Minuten.

Den Termin und die Downtime hat Hetzner eingehalten. Die neue Platte mußte ich nur noch partitionieren und ins RAID einbinden. Fertig.

    sfdisk -d /dev/sda | sfdisk /dev/sdb
    mdadm --add /dev/md0 /dev/sdb1
    mdadm --add /dev/md1 /dev/sdb2
    grub-install /dev/sdb

Ich bin sehr zufrieden mit Hetzner. Von manch anderem Massenhoster kenne ich deutlich unprofessionelleres Vorgehen bei Hardwaredefekten dieser Art.
