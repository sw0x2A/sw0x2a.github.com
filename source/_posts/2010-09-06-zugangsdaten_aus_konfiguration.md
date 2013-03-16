---
title: Zugangsdaten aus Konfiguration von Siemens-Router auslesen
author: sw
layout: post
permalink: /2010/09/zugangsdaten_aus_konfiguration/
categories:
  - Uncategorized
tags:
  - crypto
  - perl
  - security
---
# 

Hier war noch ein alter Siemens ADSL-Router (SL2-141-I) in Betrieb, der ersetzt werden sollte. Die Zugangsdaten für den DSL-Provider und DynDNS waren auf ihm konfiguriert, aber sonst unbekannt. An diese Daten zu kommen versuchte ich und zwar mit Erfolg.

Über das Webinterface konnte man ein Backup der Konfiguration anlegen. In der resultierenden XML-Datei standen auch die gesuchten Zugangsdaten, jedoch nicht im Klartext. Die Passwörter sahen aus, als wenn sie ins hexadezimale Format umgewandelt wurden. Dieses in ASCII zurückzuverwandeln sollte kein Problem sein. Das Ergebnis war jedoch nur Sonderzeichensalat. Also schaute ich mir die Daten genauer an und entdeckte eine Regelmäßigkeit. Es dauerte nicht lange und es war gelöst: Der dezimale ASCII-Wert jedes Zeichens wurde von 255 abgezogen und hexadezimal gespeichert. Mit diesem Wissen war es nun leicht die gesuchten Zugangsdaten zu ermitteln.

Die Funktion decrypt im folgenden Perl-Skript nimmt das hexadezimale Passwort entgegen und gibt es im Klartext wieder zurück. Zur Vollständigkeit gibt es auch eine Funktion zum “Verschlüsseln” (encrypt), die aber angesichts es trivialen Algorithmus besser nicht eingesetzt werden sollte.

    #!/usr/bin/perl
    
    use strict;
    
    sub encrypt {
        my $string = shift;
        my $result;
        for ( unpack "C*", $string ) {
            $result .= sprintf uc "%x", 255 - $_;
        }
        return $result;
    }
    
    sub decrypt {
        my $hex_string = shift;
        my $result;
        for ( $hex_string =~ m/../g ) {
            $result .= chr( 255 - hex $_ );
        }
        return $result;
    }
    
    print decrypt( encrypt('mysecretpassword') ) . "n";