---
title: Perl 5.10 auf Ubuntu (zusätzlich) installieren
author: sw
layout: post
permalink: /2008/05/perl_510_auf_ubuntu_zusatzlich/
categories:
  - Uncategorized
tags:
  - anleitung
  - howto
  - linux
  - perl
  - ubuntu
---
# 

Perl 5.10 wurde Ende 2007 [veröffentlicht][1], fand aber keinen Einzug in das kürzlich erschienene Ubuntu 8.04 LTS. Wer die neuen Features von Perl 5.10 ausprobieren oder nutzen möchte, muss es separat hinzuinstallieren. Die vorinstallierte Perl-Version (5.8.8) sollte bestehen bleiben und weiterhin als Standard ausgeführt werden.

 [1]: http://dev.perl.org/perl5/news/2007/perl-5.10.0.html

Zunächst wird der aktuelle Tarball von Perl 5.10 heruntergeladen und entpackt. Danach werden mit den folgenden vier Befehlen Perl kompiliert und installiert. Die [Installationsanleitung][2] erklärt die Details.

 [2]: http://search.cpan.org/~rgarcia/perl-5.10.0/INSTALL

    $ sh Configure -de
    $ make
    $ make test
    $ sudo make install PERLNAME=perl510

Mit dem Parameter PERLNAME wird der Name des Perl-Binary übergeben. So ist sichergestellt, das \`perl\` weiterhin den vorinstallierten Perl-Interpreter aufruft. Mit dem Kommando \`perl510\` wird dann Perl 5.10 ausgeführt.

    $ perl -v | head -n 2
    This is perl, v5.8.8 built for i486-linux-gnu-thread-multi
    $ perl510 -v | head -n 2
    This is perl, v5.10.0 built for i686-linux

Ein Beispielskript und seine Ausgabe:

    $ cat first.pl
    use feature ':5.10';
    say "Hello, world!";
    $ perl510 first.pl
    Hello, world!
