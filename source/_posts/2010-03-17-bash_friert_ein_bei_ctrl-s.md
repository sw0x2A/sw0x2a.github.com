---
title: Bash friert ein bei Ctrl-S
author: sw
layout: post
permalink: /2010/03/bash_friert_ein_bei_ctrl-s/
categories:
  - Uncategorized
tags:
  - bash
  - data flow control
  - linux
  - terminal
---
# 

Weil das hier gerade aufkam, kurz zur Erklärung: Wenn eine Terminalsitzung “einfriert”, hat der Benutzer vermutlich vorher die Tastenkombination Ctrl-S gedrückt. Ctrl-S steht für XOFF, das für die [Datenflusskontrolle][1] von asynchronen seriellen Verbindungen benutzt wird. Das Gegenstück ist XON (Ctrl-Q). Ich kenne das noch von den alten Modem-Verbindungen. Also: Wenn das Terminal nicht mehr reagiert, mit Ctrl-Q XON auslösen und weiter geht’s!

 [1]: http://de.wikipedia.org/wiki/Datenflusskontrolle