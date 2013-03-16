---
title: Broken cable disabled Gigabit Ethernet
author: sw
layout: post
permalink: /2011/07/broken-cable-disabled-gigabit-ethernet/
categories:
  - Uncategorized
tags:
  - cat 5e
  - ethernet
  - mii-tool
  - network
  - networking
---
# 

I have just noticed that one of our servers were connected with 100baseTx instead of 1000baseT. Both end-points support 1000baseT but it was not advertised.

    # mii-tool -v eth0
    eth0: negotiated 100baseTx-FD, link ok
      product info: vendor 00:50:43, model 11 rev 1
      basic mode: autonegotiation enabled
      basic status: autonegotiation complete, link ok
      capabilities: 1000baseT-FD 100baseTx-FD 100baseTx-HD 10baseT-FD 10baseT-HD
      advertising: 100baseTx-FD 100baseTx-HD 10baseT-FD 10baseT-HD flow-control
      link partner: 1000baseT-FD 100baseTx-FD 100baseTx-HD 10baseT-FD 10baseT-HD
    

First idea was to check cable. It was the same Cat 5e cable we always use. However, I replaced it with another one of the same type and suddenly Gigabit Ethernet was working again.

    # mii-tool eth0
    eth0: negotiated 1000baseT-FD flow-control, link ok