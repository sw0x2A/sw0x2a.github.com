---
title: Hide .svn files in Apache
author: sw
layout: post
permalink: /2011/06/hide-svn-files-in-apache/
categories:
  - Uncategorized
tags:
  - apache
  - apache2
  - httpd
  - redirectmatch
  - subversion
  - svn
---
# 

Subversion stores some metadata of working copies in hidden directories. If your deployment process uses a working copy in the document root of your Apache vhost, those files are available to the public. The best way to hide those files is to respond with HTTP error 404 (file not found).

    RedirectMatch 404 /\.svn(/|$)