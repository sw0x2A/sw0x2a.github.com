---
title: 'Munin-Plugin: vzfree'
author: sw
layout: post
permalink: /2008/03/munin-plugin_vzfree/
categories:
  - Uncategorized
tags:
  - monitoring
  - munin
  - openvz
  - rrdtool
  - scripting
  - shell
  - virtuozzo
  - vps
  - vzfree
---
# 

Die Software [Munin][1] ist ein Monitoring-Tool zur Status- und Prozess-Visualisierung und -Überwachung. Um den Speicherbedarf von [OpenVZ][2]/[Virtuozzo][3] VPS erfassen zu können, habe ich basierend auf dem populären Skript [vzfree][4] ein Plugin für Munin geschrieben. Es werden der momentan genutzte, maximal genutzte, garantierte und maximal mögliche Speicher des VPS in Megabyte (MB) erfasst.

 [1]: http://munin.projects.linpro.no/
 [2]: http://openvz.org/
 [3]: http://www.parallels.com/de/products/virtuozzo/
 [4]: http://www.google.com/search?q=vzfree

[![][6]][6]

 [6]: /assets/2008/03/vzfree-day.png

Die Installation ist einfach: Das Plugin wird im Munin Plugin-Verzeichnis (`/etc/munin/plugins/` in Debian) mit dem Namen [vzfree][7] gespeichert und ausführbar gemacht.

 [7]: https://github.com/sw0x2A/vzfree/blob/master/vzfree

    #!/bin/sh
    #
    # vzfree - Munin plugin to monitor memory in a OpenVZ/Virtuozzo environment
    #
    # Author: Stefan Warten 
    # Website: http://syslog.warten.de/2008/03/munin-plugin-vzfree.html
    # Copyright: (C) 2008 Stefan Warten
    # Licence: GPL-2 
    #
    # Usage: Place this file in the Munin plugins directory, make it executable
    # and add these lines to the Munin node config:
    #
    # [vzfree]
    # user root
    #
    # Parameters understood:
    #
    # config (required)
    # autoconf (optional - used by munin-config)
    #
    #
    # Magic markers - optional - used by installation scripts and
    # munin-config:
    #
    #%# family=auto
    #%# capabilities=autoconf
    
    
    if [ "$1" = "autoconf" ]; then
    if [ -r /proc/user_beancounters ]; then
    echo yes
                    exit 0
            else
    echo no
                    exit 1
            fi
    fi
    
    if [ "$1" = "config" ]; then
    echo 'graph_title vzfree memory usage'
            echo 'graph_order limit maxheld held guaranteed'
            echo 'graph_args -l 0'
            echo 'graph_category system'
            echo 'graph_vlabel MB'
            echo 'graph_period minute'
            echo 'held.label held'
            echo 'held.type GAUGE'
            echo 'held.info Used memory'
            echo 'maxheld.label maxheld'
            echo 'maxheld.type GAUGE'
            echo 'maxheld.info Maximum used memory'
            echo 'guaranteed.label guaranteed'
            echo 'guaranteed.type GAUGE'
            echo 'guaranteed.info Out-of-memory kill guarantee'
            echo 'limit.label maximum'
            echo 'limit.type GAUGE'
            echo 'limit.info Memory allocation limit'
            exit 0
    fi
    
    x=$(cat /proc/user_beancounters | grep privvmpages | 
            awk 'BEGIN{ cur=max=lim=0; }{ cur =$2;max =$3;lim =$5 } 
    END {print cur*4/1024,max*4/1024,lim*4/1024}')
    guaranteed=$(cat /proc/user_beancounters | grep oomguarpages | 
            awk 'BEGIN{ cur=max=lim=0; }{ lim =$4 } END { print lim*4/1024}')
    
    held=$(echo ${x} | awk '{print $1}')
    maxheld=$(echo ${x} | awk '{print $2}')
    limit=$(echo ${x} | awk '{print $3}')
    
    echo "held.value ${held}"
    echo "maxheld.value ${maxheld}"
    echo "guaranteed.value ${guaranteed}"
    echo "limit.value ${limit}"
    

Weil die Datei `/proc/user_beancouters` nur von root gelesen werden kann, muss das Plugin mit den Rechten von root ausgeführt werden. Dazu werden in der Konfigurationsdatei des Munin-Nodes (in Debian ist das `/etc/munin/plugin-conf.d/munin-node`) noch die folgenden Zeilen eingefügt:

    [vzfree]
    user root
    

Nach einem Neustart des Munin-Nodes wird nun der Arbeitsspeicher des VPS überwacht und visualisiert.
