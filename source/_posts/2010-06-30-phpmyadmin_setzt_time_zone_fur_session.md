---
title: phpMyAdmin setzt time_zone für Session
author: sw
layout: post
permalink: /2010/06/phpmyadmin_setzt_time_zone_fur_session/
categories:
  - Uncategorized
tags:
  - aws
  - mysql
  - phpmyadmin
  - rdb
  - time_zone
---
# 

Simpler Patch, damit phpMyAdmin für jede Verbindung zu Datenbankservern die Zeitzone setzt. Das war hier erforderlich, weil eine Änderung der default time_zone bei AWS RDB nicht möglich ist.

    --- libraries/dbi/mysqli.dbi.lib.php        2010-06-26 12:53:06.000000000  0200
        libraries/dbi/mysqli.dbi.lib.php-new    2010-06-30 16:23:54.000000000  0200
    @@ -137,6  137,10 @@
             PMA_DBI_postConnect($link, $is_controluser);
         }
    
         // Dirty hack to set session time_zone because we cannot
         // change default time_zone in AWS RDB.
         mysqli_query($link, "SET time_zone = 'Europe/Berlin';");
     
         return $link;
     }