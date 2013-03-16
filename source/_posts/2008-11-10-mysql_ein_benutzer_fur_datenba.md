---
title: 'MySQL: Ein Benutzer für Datenbanknamensschema'
author: sw
layout: post
permalink: /2008/11/mysql_ein_benutzer_fur_datenba/
categories:
  - Uncategorized
tags:
  - grant
  - mysql
  - privileges
---
# 

Will man einen Benutzer für beliebig viele Datenbanken eines bestimmten Namensschemas anlegen, also für beispielsweise db\_1, db\_2, db_3, …, kann der variable Teil im Datenbanknamen durch die wildcard % ersetzt werden:

    GRANT ALL ON `db_%`.* TO 'user'@'localhost' IDENTIFIED BY 'password';
