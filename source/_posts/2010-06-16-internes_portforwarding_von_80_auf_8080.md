---
title: Internes Portforwarding von 80 auf 8080
author: sw
layout: post
permalink: /2010/06/internes_portforwarding_von_80_auf_8080/
categories:
  - Uncategorized
tags:
  - iptables
  - linux
  - network
  - networking
---
# 

`
iptables -t nat -I PREROUTING --src 0/0 --dst 127.0.0.1 -p tcp --dport 80 -j REDIRECT --to-ports 8080
`