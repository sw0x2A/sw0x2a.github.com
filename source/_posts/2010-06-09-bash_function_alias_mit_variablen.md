---
title: 'BASH function: Alias mit Variablen'
author: sw
layout: post
permalink: /2010/06/bash_function_alias_mit_variablen/
categories:
  - Uncategorized
tags:
  - alias
  - bash
  - function
---
# 

Um häufig benutzte, komplizierte Shell-Kommandos abzukürzen, kann man ein kurzes `alias` definieren. Leider können so keine Parameter bzw. Variablen übergeben werden. Alternativ kann man in seiner `.bashrc` eine Funktion schreiben:

    function sshlnxp () { 
        ssh -A user@lnxp-$*.srv.mediaways.net;
    }