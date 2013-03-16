---
title: Backup/Restore mit dd oder tar über SSH
author: sw
layout: post
permalink: /2008/09/backuprestore_mit_dd_oder_tar/
categories:
  - Uncategorized
tags:
  - backup
  - dd
  - hdd
  - io
  - linux
  - loopback
  - restore
  - smart
  - ssh
  - storage
  - tar
---
# 

Die Festplatte einer Workstation machte Zicken. [S.M.A.R.T.][1] und dmesg bestätigten einen Hardwaredefekt des Mediums.

 [1]: http://de.wikipedia.org/wiki/Self-Monitoring,_Analysis_and_Reporting_Technology

    # smartctl -a /dev/sdb
    smartctl version 5.37 [i686-pc-linux-gnu] Copyright (C) 2002-6 Bruce Allen
    Home page is http://smartmontools.sourceforge.net/
    === START OF INFORMATION SECTION ===
    Model Family:     Maxtor MaXLine III family (SATA/300)
    Device Model:     Maxtor 7V300F0
    Serial Number:    V605EYSG
    Firmware Version: VA111630
    User Capacity:    300.090.728.448 bytes
    Device is:        In smartctl database [for details use: -P show]
    ATA Version is:   7
    ATA Standard is:  ATA/ATAPI-7 T13 1532D revision 0
    Local Time is:    Sun Sep 28 14:09:35 2008 CEST
    SMART support is: Available - device has SMART capability.
    SMART support is: Enabled
    === START OF READ SMART DATA SECTION ===
    SMART overall-health self-assessment test result: FAILED!
    Drive failure expected in less than 24 hours. SAVE ALL DATA.
    See vendor-specific Attribute list for failed Attributes.
    [...]
    SMART Attributes Data Structure revision number: 32
    Vendor Specific SMART Attributes with Thresholds:
    ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
    [...]
    5 Reallocated_Sector_Ct   0x0033   016   016   063    Pre-fail  Always   FAILING_NOW 3003
    [...]
    SMART Self-test log structure revision number 1
    Num  Test_Description    Status                  Remaining  LifeTime(hours)  LBA_of_first_error
    # 1  Extended offline    Completed: read failure       20%      7576         420064150
    [...]
    # dmesg
    [...]
    [25722.726055] ata3.01: exception Emask 0x0 SAct 0x0 SErr 0x0 action 0x0
    [25722.726105] ata3.01: BMDMA stat 0x64
    [25722.726170] ata3.01: cmd 25/00:f8:50:ab:09/00:00:19:00:00/f0 tag 0 dma 126976 in
    [25722.726201]          res 51/40:f8:96:ab:09/40:00:19:00:00/f0 Emask 0x9 (media error)
    [25722.726247] ata3.01: status: { DRDY ERR }
    [25722.726270] ata3.01: error: { UNC }
    [25722.852221] ata3.00: configured for UDMA/133
    [25722.868931] ata3.01: configured for UDMA/133
    [25722.869026] ata3: EH complete
    [...]
    [25739.235989] sd 2:0:1:0: [sdb] Result: hostbyte=DID_OK driverbyte=DRIVER_SENSE,SUGGEST_OK
    [25739.236049] sd 2:0:1:0: [sdb] Sense Key : Medium Error [current] [descriptor]
    [25739.236107] Descriptor sense data with sense descriptors (in hex):
    [25739.236141]         72 03 11 04 00 00 00 0c 00 0a 80 00 00 00 00 00
    [25739.236266]         19 09 ab 96
    [25739.236316] sd 2:0:1:0: [sdb] Add. Sense: Unrecovered read error - auto reallocate failed
    [25739.236381] end_request: I/O error, dev sdb, sector 420064150
    [25739.236423] Buffer I/O error on device sdb, logical block 52508018
    [25739.236472] Buffer I/O error on device sdb, logical block 52508019
    [25739.236515] Buffer I/O error on device sdb, logical block 52508020
    [25739.236553] Buffer I/O error on device sdb, logical block 52508021
    [25739.236595] Buffer I/O error on device sdb, logical block 52508022
    [25739.236637] Buffer I/O error on device sdb, logical block 52508023
    [25739.236683] Buffer I/O error on device sdb, logical block 52508024
    [25739.236721] Buffer I/O error on device sdb, logical block 52508025
    [25739.236760] Buffer I/O error on device sdb, logical block 52508026
    [25739.236805] Buffer I/O error on device sdb, logical block 52508027
    [25739.237034] ata3: EH complete
    [25739.322135] sd 2:0:1:0: [sdb] 586114704 512-byte hardware sectors (300091 MB)
    [25739.332778] sd 2:0:1:0: [sdb] Write Protect is off
    [25739.332823] sd 2:0:1:0: [sdb] Mode Sense: 00 3a 00 00
    [25740.201637] sd 2:0:1:0: [sdb] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
    [25740.212284] sd 2:0:0:0: [sda] 312500000 512-byte hardware sectors (160000 MB)
    [25740.212482] sd 2:0:0:0: [sda] Write Protect is off
    [25740.212516] sd 2:0:0:0: [sda] Mode Sense: 00 3a 00 00
    [25740.212767] sd 2:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA

Es sollte eine komplette Sicherung des Datenträgers erstellt werden. Zunächst wurden alle Partitionen des Device ausgehängt, um jede weitere Veränderung der Daten möglichst zu vermeiden. Auf einen Dateisystemcheck wurde deshalb vorerst verzichtet. Danach wurde eine 1:1-Kopie der Festplatte als Image auf den Fileserver verschoben. Mit dd und ssh ist das einfach über eine Pipe zu realisieren. Da auf dem defekten Medium mit Lesefehlern zu rechnen ist, wird dd angewiesen, Fehler zu ignorieren.

    # dd if=/dev/sdb conv=noerror,sync | ssh user@hostname "dd of=/data/hostname-device-date.img"

Alternativ kann eine direkte Sicherung der Dateien bei gemounteten Dateisystem erstellt werden. In diesem Fall ist das jedoch nicht zu empfehlen.

    # tar czf - /media/sdb1 | ssh user@hostname "cat > /data/hostname-device-date.tar.gz"

Als das so erstellte Image gesichert war, konnte die Wiederherstellung der Daten beginnen. Um einen Datenverlust zu vermeiden, sollte das Image nur read-only verwendet werden. Besser ist mit Kopien der Sicherung zu arbeiten.

    # losetup --read-only /dev/loop0 /data/hostname-device-date.img
    # fdisk -l /dev/loop0
    Disk /dev/loop0: 226.2 GB, 226256034816 bytes
    255 heads, 63 sectors/track, 27507 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Disk identifier: 0x9badd7b7
    Device Boot      Start         End      Blocks   Id  System
    /dev/loop0p1               1       36483   293049666    c  W95 FAT32 (LBA)

Um nun eine Partition mounten zu können, muss ermittelt werden, ab welchem Byte die Partition im Image beginnt. Dieses Offset ist das Produkt aus der Anzahl der Köpfe (heads), den Sektoren pro Track (sectors/track), der Größe eines Blocks in Byte und der Nummer des ersten Zylinders der zu mountenden Partition:

    255 heads * 63 sectors/track * 512 bytes * 1 = 8225280

Das Ergebnis wird als Offset an losetup übergeben.

    # losetup -d /dev/loop0
    # losetup -o8225280 /dev/loop0 /data/hostname-device-date.img

Nun lässt sich mit dem Device /dev/loop0 wie mit der ursprünglichen Partition umgehen. Es kann nun beispielsweise ein Dateisystemcheck durchgeführt und danach die Partition gemountet werden.

    # fsck.vfat /dev/loop0
    # mount /dev/loop0 /mnt/mountpoint

Wurde die defekte Festplatte ersetzt, kann die Sicherung auch einfach zurückkopiert werden.

    # ssh user@hostname "dd if=/data/hostname-device-date.img" | dd of=/dev/sdb
    # ssh user@hostname "cat /data/hostname-device-date.tar.gz" | tar xzf -
