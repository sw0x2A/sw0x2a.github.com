---
title: Bug im Perl-RPM von RHEL5
author: sw
layout: post
permalink: /2008/09/bug_im_perlrpm_von_rhel5/
categories:
  - Uncategorized
tags:
  - bugs
  - linux
  - perl
  - red hat
  - rhel5
---
# 

Durch einen [Blog-Artikel][1] kam ich einem ungewöhnlichen Verhalten einiger Perl-Skripte auf unseren Servern auf die Spur. Das Perl-Paket von RHEL5 hat einen [Bug][2], der zur Folge hat, dass Prozesse, die bless und overload benutzen, ausgebremst werden. Red Hat bietet derzeit kein offizielles Bugfix an. Über unseren Hoster stellte Red Hat uns dennoch ein Bugfix zur Verfügung, das das Performance-Problem löst.

 [1]: http://blog.vipul.net/2008/08/24/redhat-perl-what-a-tragedy/
 [2]: https://bugzilla.redhat.com/show_bug.cgi?id=379791