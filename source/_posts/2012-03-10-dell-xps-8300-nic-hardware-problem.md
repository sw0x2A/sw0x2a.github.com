---
title: DELL XPS 8300 NIC hardware problem
author: sw
layout: post
permalink: /2012/03/dell-xps-8300-nic-hardware-problem/
categories:
  - Uncategorized
tags:
  - dell
  - hardware
  - nic
---
# 

It seems like there is a hardware problem at least on some motherboards of [DELL XPS 8300][1]. It appears to be an initialization issue of the network chipset (Broadcom NetLink BCM57788 Gigabit) when you turn on the computer at low temperatures. When OS starts, the NIC is not detected and therefore not usable. As soon as the PC warms up (usually after some minutes), you can restart and the problem is gone until next cold restart. 

 [1]: http://en.community.dell.com/support-forums/desktop/f/3514/p/19389902/19906490.aspx

Seriously, those PCs needs preheating like an old diesel engine. Since there is no choke, you can try with a hair dryer instead. Let’s see how DELL support handles this…