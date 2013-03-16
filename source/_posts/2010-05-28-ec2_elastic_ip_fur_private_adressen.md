---
title: 'EC2: Elastic IP für private Adressen'
author: sw
layout: post
permalink: /2010/05/ec2_elastic_ip_fur_private_adressen/
categories:
  - Uncategorized
tags:
  - amazon
  - aws
  - cloud
  - ec2
  - elastic ip
---
# 

Die Instanzen im Amazon EC2 bekommen dynamische Hostnamen und IP-Adressen. Zur dauerhaften Adressierung kann man Elastic IPs allocieren, die jedoch öffentliche Adressen sind. Will man auf eine andere Instanz in der Cloud zugreifen, möchte man selbstverständlich die lokale IP (RFC1918) nutzen und keine öffentliche (das ist ja auch eine Kostenfrage). Doch Amazon bietet hier eine Möglichkeit: Der Reverse-DNS-Hostname der IP-Adresse wird von den Amazon-Nameservern auf die private Adresse aufgelöst, während er sonst auf die Öffentliche zeigt.

    root@domU-xx-xx-xx-xx-xx-xx:~# host 184.73.175.xxx
    xxx.175.73.184.in-addr.arpa domain name pointer ec2-184-73-175-xxx.compute-1.amazonaws.com.
    root@domU-xx-xx-xx-xx-xx-xx:~# host ec2-184-73-175-xxx.compute-1.amazonaws.com
    ec2-184-73-175-xxx.compute-1.amazonaws.com has address 10.253.87.xxx
    sw@external:~$ host ec2-184-73-175-xxx.compute-1.amazonaws.com
    ec2-184-73-175-xxx.compute-1.amazonaws.com has address 184.73.175.xxx

Mit dem Reverse-DNS-Eintrag der Elastic IP hat man also auch eine statische Adresse für internen Traffic im EC2.
