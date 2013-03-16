---
title: sudo mit SSH auf remote hosts
author: sw
layout: post
permalink: /2008/12/sudo_mit_ssh_auf_remote_hosts/
categories:
  - Uncategorized
tags:
  - linux
  - remote
  - root
  - security
  - ssh
  - sudo
---
# 

Mit SSH lassen sich Kommandos direkt auf entfernten Rechnern ausführen. Werden für das Kommando die Rechte des Superusers benötigt (und man kann sich nicht direkt als root anmelden), kann in den meisten Fällen sudo nicht sofort helfen.

    $ ssh host "sudo mycommand"
    sudo: sorry, you must have a tty to run sudo

Aus Sicherheitsgründen ist bei den meisten Linux-Distributionen in der Konfiguration von sudo mit ‘requiretty’ das Vorhandensein einer Terminal-Sitzung verpflichtend, wie sie auch nach einer Anmeldung mittels SSH besteht.

    # grep requiretty /etc/sudoers
    Defaults    requiretty

Im Fall des mit SSH übermitteln Kommandos findet jedoch üblicherweise keine tty-Belegung statt. Forcieren kann man eine pseudo-tty allocation mit dem Parameter `-t` in SSH und dann funktioniert auch sudo bei der Kommandoübergabe mit SSH.

    $ ssh -t hostname "sudo mycommand"
