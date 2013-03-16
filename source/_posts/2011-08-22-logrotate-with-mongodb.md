---
title: Logrotate with MongoDB
author: sw
layout: post
permalink: /2011/08/logrotate-with-mongodb/
categories:
  - Uncategorized
tags:
  - logrotate
  - mongodb
---
# 

MongoDB packages are shipped with [logging][1] enabled in configuration but without a script to rotate the logfile. There are two build-in ways to let MongoDB rotate its logfile. You can execute `db.runCommand("logRotate");` from the mongo shell or `kill -SIGUSR1 $(cat /var/lib/mongodb/mongod.lock)` from unix shell.

 [1]: http://www.mongodb.org/display/DOCS/Logging

The best way to automate this is to use `logrotate`. Copy the following code into `/etc/logrotate.d/mongodb` and make sure that pathes and filenames in the script correspond with those in your system.

    /var/log/mongodb/mongodb.log {
        daily
        rotate 7
        compress
        missingok
        notifempty
        sharedscripts
        postrotate
            /bin/kill -SIGUSR1 `cat /var/lib/mongodb/mongod.lock` && 
            rm -f /var/log/mongodb/mongodb.log.????-??-??T??-??-??
        endscript
    }
    

It rotates the logfile `/var/log/mongodb/mongodb.log` on a daily basis and keeps them for 7 days compressed. Since MongoDB’s build-in `logRotate` conflicts with `logrotate`, it also cleans up the empty, log-rotated files created by MongoDB.