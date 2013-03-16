---
title: 'Fetch IP addresses of Pingdom&#8217;s probe servers'
author: sw
layout: post
permalink: /2011/12/fetch-ip-addresses-of-pingdoms-probe-servers/
categories:
  - Uncategorized
tags:
  - monitoring
  - perl
  - pingdom
  - regex
---
# 

    wget --quiet -O- https://www.pingdom.com/rss/probe_servers.xml | perl -nle 'print $1 if /IP: (([01]?dd?|2[0-4]d|25[0-5]).([01]?dd?|2[0-4]d|25[0-5]).([01]?dd?|2[0-4]d|25[0-5]).([01]?dd?|2[0-4]d|25[0-5]));/'