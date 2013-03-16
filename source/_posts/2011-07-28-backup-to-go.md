---
title: Backup To Go
author: sw
layout: post
permalink: /2011/07/backup-to-go/
categories:
  - Uncategorized
tags:
  - backup
  - bacula. tw_cli
---
# 

We are using Bacula to store backups of all our servers on one big storage system. That made us sleep better for some days but then we thought it would be good to have another backup at a remote site (because a fire or another disaster could destroy servers and backup at once). Usually, one would just copy the backups over network to that remote site. Unfortunally, this was not an option for us because of some special conditions not worth to mention here. We decided to carry backup hard drives to that remote site instead.

The backup system is a 2U server with 12 hot swappable hard drive bays. 10 of them are bundled as RAID-5 to store all full (weekly) and incremental (daily) backups. The other two hard drives are mirred (RAID-1) and have the latest full backups saved on it. Once a week, we replace one hard drive from RAID-1 and carry it to that remote site. That is it: A remote backup copy with almost no extra costs.

Backups are done by Bacula automatically. All we have to do is to remove one hard drive from backup server and replace it. To make this easy, I wrote a small script that helps us doing it. It uses tw_cli, the monitoring and management software for 3ware RAID controllers. There are also Debian packages and [repositories][1] available.

 [1]: http://jonas.genannt.name/

    #!/bin/bash
    
    TW_CLI=/usr/sbin/tw_cli
    DATE_CMD=/bin/date
    WEEK=$($DATE_CMD  %V | sed s/^0//)
    let "DRIVE=($WEEK%2) 18"
    CONTROLLER="/c0"
    SHOW_RAID_STATUS="$TW_CLI $CONTROLLER show"
    RAID_CHECK_CMD="$TW_CLI /c0 show allunitstatus"
    REMOVE_DRIVE="$TW_CLI $CONTROLLER/p$DRIVE remove quiet"
    
    $SHOW_RAID_STATUS
    
    if $RAID_CHECK_CMD | grep -q 'Not Optimal Units = [^0]'; then
    	echo "RAID status is not optimal. Aborting."; echo
    	exit 1
    fi
    
    echo "You are about to remove VPort p$DRIVE. This will degrade RAID status!"
    read -p "Are you sure? (yes/no)"
    if [ "$REPLY" == "yes" ]; then
    	$REMOVE_DRIVE
    fi
    

It checks RAID status (line 14) and if it is in an optimal state, it chooses a hard drive and removes it (line 22). Because we do not want to change the same hard drive all the time, we choice hard drive by whether calendar week is odd or even (line 6).