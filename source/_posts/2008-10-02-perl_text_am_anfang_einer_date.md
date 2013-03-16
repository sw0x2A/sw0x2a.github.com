---
title: 'Perl: Text am Anfang einer Datei einfügen'
author: sw
layout: post
permalink: /2008/10/perl_text_am_anfang_einer_date/
categories:
  - Uncategorized
tags:
  - one liner
  - perl
  - shell
  - text manipulation
---
# 

    perl -i -pe 'print "Dieser Text soll in die erste Zeilen" if $. == 1' filename
