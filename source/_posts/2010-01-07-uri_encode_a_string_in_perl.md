---
title: 'URI encode a string in Perl:'
author: sw
layout: post
permalink: /2010/01/uri_encode_a_string_in_perl/
categories:
  - Uncategorized
tags:
  - encoding
  - perl
  - uri
---
# 

    $string =~ s/([^A-Za-z0-9])/sprintf("%%X", ord($1))/seg;