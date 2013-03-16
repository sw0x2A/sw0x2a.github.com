---
title: 'Perl: Daten nach Häufigkeit sortieren'
author: sw
layout: post
permalink: /2008/09/perl_daten_nach_haufigkeit_sor/
categories:
  - Uncategorized
tags:
  - hash
  - perl
  - sort
  - value
---
# 

Die Aufgabe war, eine Liste von IP-Adressen einzulesen und nach Häufigkeit (numerisch) absteigend zu sortieren. Wie man hash values sortiert, wollte mir im entscheidenen Augenblick nicht einfallen und so hat die Lösung eine Viertelstunde auf sich warten lassen. Soll nicht wieder vorkommen.

    #!/usr/bin/perl
    my %counter;
    while () {
        chomp( my $ip_address = $_ );
        $counter{$ip_address}  = 1;
    }
    for my $ip_address ( sort { $counter{$b}  $counter{$a} } keys %counter ) {
        print "$ip_address: $counter{$ip_address}n";
    }