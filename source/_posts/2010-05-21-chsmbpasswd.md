---
title: chsmbpasswd
author: sw
layout: post
permalink: /2010/05/chsmbpasswd/
categories:
  - Uncategorized
tags:
  - bash
  - samba
  - smbpasswd
---
# 

    #!/bin/bash
    # Reads username:password per line in plain text from stdin
    # and feeds smbpasswd to add/update users, similiar to chpasswd
    while read line
    do
        username="$(echo ${line} | cut -d: -f1)"
        password="$(echo ${line} | cut -d: -f2)"
        (echo $password; echo $password) | smbpasswd -s -a $username
    done