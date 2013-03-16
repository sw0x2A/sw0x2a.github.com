---
title: Install MGSE on other distributions than Linux Mint
author: sw
layout: post
permalink: /2011/12/install-mgse-on-other-distributions-than-linux-mint/
categories:
  - Uncategorized
tags:
  - fedora
  - gnome3
  - linux mint
  - mgse
---
# 

The friendly guys from [Linux Mint][1] wrote some Gnome Shell extentions which make Gnome 3 feel more like Gnome 2. The good thing is that you can benefit from that even if you are using a different distribution. First of all, we will install the [Linux Mint Shell Extensions for Gnome 3 (MGSE)][2].

 [1]: http://linuxmint.com/
 [2]: https://github.com/linuxmint/MGSE

    $ git clone https://github.com/linuxmint/MGSE.git
    $ cd MGSE/
    $ ./test
    

To make the Gnome Shell load the new extentions, reload Gnome Shell by hitting `ALT-F2` and executing `r`.

Use `gnome-tweak-tool` to activate the extentions you want.