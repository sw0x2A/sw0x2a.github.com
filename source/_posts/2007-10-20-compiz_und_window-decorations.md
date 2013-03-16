---
title: Compiz und Window-Decorations
author: sw
layout: post
permalink: /2007/10/compiz_und_window-decorations/
categories:
  - Uncategorized
tags:
  - compiz
  - linux
  - nvidia
  - ubuntu
  - window-decorator
  - x11
---
# 

Auf einem Ubuntu Linux mit Nvidia-Grafikkarte war es der in die Datei `/etc/X11/xorg.conf` (Section “Screen”) hinzugefügte Eintrag

    Option “AddARGBGLXVisuals” “True” 

der Compiz dazu bewegte, den Desktop mit Window-Decorations zu zeichnen.
