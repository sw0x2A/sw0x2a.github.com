---
title: VMware vs. ntpd
author: sw
layout: post
permalink: /2008/09/vmware_vs_ntpd/
categories:
  - Uncategorized
tags:
  - ntp
  - ntpd
  - synchronisation
  - virtual machines
  - virtualisierung
  - vmware
---
# 

Die Kombination VMware Server und ntpd in der virtual machine ist nicht zu empfehlen. VMware fängt die timer interrupts ab und manipuliert sie zur Lastverteilung zwischen den virtuellen Maschinen. Der ntpd kommt dadurch gehörig aus dem Takt und VMware hat Last, wo es eigentlich keine gibt.