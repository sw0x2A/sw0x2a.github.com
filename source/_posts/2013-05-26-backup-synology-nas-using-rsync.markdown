---
layout: post
title: "Backup Synology NAS using rsync"
date: 2013-05-26 10:29
categories: 
---

Synology NAS devices offer a [built-in service][1] to backup all configuration, applications and shared folders to a remote rsync server. Below is a short howto describing the steps required to setup the remote rsync services on a CentOS 6 server.

[1]: http://forum.synology.com/wiki/index.php/Backup_the_Synology_server_to_a_remote_Synology_server_or_RSync_Server

Install rsync and xinetd.

    yum install rsync xinetd

Create configuration file.

    # /etc/rsyncd.conf
    gid = nobody
    uid = nobody
    read only = no
    use chroot = yes 
    transfer logging = true
    log format = %h %o %f %l %b
    log file = /var/log/rsyncd.log
    secrets file = /etc/rsyncd.secrets
    hosts allow = 192.168.1.0/24
    
    [synology]
    comment = Backup of Synology
    path = /data/synology
    auth users = synology

Create password file.

    # /etc/rsyncd.secrets 
    synology:plaintextpassword

Restrict its file permissions.

    chmod 600 /etc/rsyncd.secrets

Enable rsync service in xinetd by setting `disabled = no` in `/etc/xinetd.d/rsync` and restart xinetd.

Open rsync port in iptables and make the change permanent.

    iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 873 -j ACCEPT
    service iptables save

Finally, fix selinux config and context of the destination directory.

    setsebool -P allow_rsync_anon_write=1
    chcon -t public_content_rw_t /data/synology

The Synology NAS should be able to establish a connection to the remote rsync server and do backups.
