---
title: mysqldump mit where clause
author: sw
layout: post
permalink: /2010/06/mysqldump_mit_where_clause/
categories:
  - Uncategorized
tags:
  - database
  - mysql
  - mysqldump
---
# 

Soll nur eine Teilmenge einer Tabelle gesichert werden, kann auch dies mit mysqldump erledigt werden. Es bietet hierzu den Parameter `--where` an, mit dem man eine übliche where clause setzen kann.

`mysqldump --where "column=1" dbname tablename`