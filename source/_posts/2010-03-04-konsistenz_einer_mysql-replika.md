---
title: Konsistenz einer MySQL-Replikation testen
author: sw
layout: post
permalink: /2010/03/konsistenz_einer_mysql-replika/
categories:
  - Uncategorized
tags:
  - datenbank
  - maatkit
  - mk-table-checksum
  - mysql
  - replikation
---
# 

Einen einfachen und schnellen Weg, die Datenkonsistenz einer Replikation in MySQL zu überprüfen, bietet mk-table-checksum aus [Maatkit][1]. Das Perl-Skript erstellt Prüfsummen der Tabellen und schreibt diese in eine eigene Tabelle. Durch die Replikation wird diese Tabelle auf den Slave übertragen und dort ebenfalls die Prüfsummen ermittelt. Hinterher lassen sich auf dem Slave die Prüfsummen vergleichen und eventuelle Veränderungen der Datenbestände feststellen. Diese lassen sich dann beispielsweise mit mk-table-sync aus der selben Tool-Sammlung wieder synchronisieren.

 [1]: http://www.maatkit.org/

Bevor es losgeht, muß auf dem Master eine neue Datenbank (hier `test`) und die folgende Tabelle erstellt werden.

    CREATE TABLE checksum (
        db         char(64)     NOT NULL,
        tbl        char(64)     NOT NULL,
        chunk      int          NOT NULL,
        boundaries char(100)    NOT NULL,
        this_crc   char(40)     NOT NULL,
        this_cnt   int          NOT NULL,
        master_crc char(40)         NULL,
        master_cnt int              NULL,
        ts         timestamp    NOT NULL,
        PRIMARY KEY (db, tbl, chunk)
    );
    

Danach wird das Skript ausgeführt.

`mk-table-checksum --replicate=test.checksum h=localhost,u=username,p=password,S=/data/db/mysql.sock`

Ist das Programm durchgelaufen, kann man hinterher auf dem Slave prüfen, ob die Prüfsummen von Master und Slave abweichend sind.

    SELECT db, tbl, chunk, this_cnt-master_cnt AS cnt_diff,
        this_crc  master_crc OR ISNULL(master_crc)  ISNULL(this_crc)
            AS crc_diff
    FROM checksum
    WHERE master_cnt  this_cnt OR master_crc  this_crc
        OR ISNULL(master_crc)  ISNULL(this_crc);