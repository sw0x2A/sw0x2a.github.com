---
title: Instant query log for MySQL
author: sw
layout: post
permalink: /2011/09/instant-query-log-for-mysql/
categories:
  - Uncategorized
tags:
  - maatkit
  - mysql
  - tcpdump
---
# 

MySQL has [build-in logging][1] for queries (general) and slow queries (slow). You can enable them at startup. It is not recommended to enable general logs on a high-traffic database server for performance reasons but sometimes you may want to be able to see what queries are executed there. 

 [1]: http://dev.mysql.com/doc/refman/5.5/en/server-logs.html

Since [mk-query-digest][2] is able to read [tcpdumps][3], it is the perfect tool to create an instant query log. Just pipe all network traffic on MySQL port to mk-query-digest for analysis.

 [2]: http://www.maatkit.org/doc/mk-query-digest.html
 [3]: http://www.tcpdump.org/

    tcpdump -i eth2 port 3306 -s 65535  -x -n -q -tttt | 
      mk-query-digest --type tcpdump
    

There are much more things you can do with mk-query-digest, so it is worth to write a dump of network traffic to a file and work with it.

    tcpdump -i eth2 port 3306 -s 65535  -x -n -q -tttt > tcpdump.out
    

You can convert it to slow query log format and parse it with your favorite analysis tool.

    mk-query-digest --type tcpdump --print --noreport < tcpdump.out > slow.log
    

The report of an analysis of mk-query-digest fingerprints queries. To search for them in your logfile, you can use the `--filter` parameter.

    mk-query-digest slow.log --no-report --print 
    --filter '$event->{fingerprint} && make_checksum($event->{fingerprint}) eq "76A68B0365255C58"'