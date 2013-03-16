---
title: Die Vorratsdatenspeicherung umgedreht
author: sw
layout: post
permalink: /2010/02/die_vorratsdatenspeicherung_um/
categories:
  - Uncategorized
tags:
  - abmahnwahn
  - linux
  - mysql
  - perl
  - vorratsdatenspeicherung
---
# 

Wer im Internet unterwegs ist, hinterläßt dabei seine Spuren unter anderem in Form der IP-Adresse des verwendeten Zugangs. Diese Adressen werden von den Providern zum Zweck der Strafverfolgung gespeichert (siehe Vorratsdatenspeicherung). Außerdem kann die IP-Adresse vom Kommunikationspartner, also Webservern, Chatkontakten, Teilnehmern von P2P-Netzwerken usw. gespeichert werden.

Am Wochenende las ich einen haarsträubenden Bericht über den Umgang mit IP-Adressen in straf- und zivilrechtlichen Prozessen, wodurch Unschuldige wegen unbeweisbarer Behauptungen und Übertragungsfehlern zu Unrecht unschöner Dinge verdächtigt wurden. Dabei kam mir die Idee, die Vorratsdatenspeicherung umzudrehen und die von mir benutzten IP-Adressen selbst zu speichern. Sollte ich einmal in die unangenehme Situation kommen, solchen Verdächtigungen ausgesetzt zu sein, kann ich wenigstens für mich selbst überprüfen, ob es wirklich von meinem Anschluß geschah. Ob das juristisch von Bedeutung und bei der eigenen Verteidigung verwertbar ist, weiß ich nicht. Aber es würde wenigstens die eigenen Zweifel ausräumen.

Daher habe ich also eine kleine Lösung entwickelt. Benötigt wird in diesem Fall ein Linux-System mit Perl und MySQL sowie ein Webserver, der CGIs (ebenfalls Perl) ausführen kann.

Zunächst wird ein Datenbankschema mit einer Tabelle angelegt. Die Tabelle wird später zur Speicherung der IP-Adressen und Start- sowie Enddatum deren Nutzung gebraucht.

    CREATE DATABASE `remote_addr_logger` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
    USE `remote_addr_logger`;
    CREATE TABLE IF NOT EXISTS `remote_addr` (
      `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
      `remote_addr` int(10) unsigned NOT NULL COMMENT 'Remote ip address. Use INET_NTOA() to convert.',
      `date_start` datetime NOT NULL COMMENT 'Address used between ...',
      `date_end` datetime NOT NULL COMMENT 'and ...',
      PRIMARY KEY (`id`),
      KEY `remote_addr` (`remote_addr`),
      KEY `date_start` (`date_start`),
      KEY `date_end` (`date_end`)
    ) ENGINE=MyISAM  DEFAULT CHARSET=utf8;
    

Für den Zugriff auf die Datenbank wird noch ein Benutzer angelegt.

    GRANT SELECT , INSERT , UPDATE ON `remote_addr_logger` . * TO 'remote_addr'@'localhost' IDENTIFIED BY '***';
    

Als Nächstes braucht es eine Möglichkeit, die IP-Adresse seines Gateways (DSL-Router etc.) zu ermitteln. Hier gibt es viele Möglichkeiten mit verschiedenen Vor- und Nachteilen. Ich entschied mich kurzerhand für ein winziges CGI-Skript, das später vom Hauptprogramm via HTTP request aufgerufen werden soll und die eigene remote address in einem kleinen XML output ausgibt.

    #!/usr/bin/perl
    
    my $remote_addr = $ENV{'REMOTE_ADDR'};
    
    print "Content-type: text/htmlnn";
    print "n";
    print "$remote_addrn";
    

Nun fehlt nur noch das Hauptprogramm, das das CGI-Skript aufruft, die XML-Datei empfängt und auswertet und die IP-Adresse samt Datum in die Datenbank schreibt. Hier sind zu Beginn die URL des CGI-Skripts sowie die Zugangsdaten zur Datenbank einzutragen.

    #!/usr/bin/perl
    
    use strict;
    use warnings;
    use LWP::Simple;
    use DBI;
    
    my $get_remote_addr_cgi_url = 'http://example.com/cgi-bin/get_remote_addr.cgi';
    
    my $db_name = 'remote_addr_logger';
    my $db_host = 'localhost';
    my $db_user = 'remote_addr';
    my $db_pass = 'yoursecretpassword';
    
    sub parse_xml_data {
        my $xml_data = shift;
        my $tag = 'remote_addr';
        my $inet_addr = '(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?).){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)';
        my $pattern = "($inet_addr)";
        if ( $xml_data =~ /$pattern/ ) {
            my $remote_addr = $1;
            return $remote_addr;
        } else {
            return -1;
        }
    }
    
    sub get_remote_addr {
        my $xml_data = get($get_remote_addr_cgi_url);
        if ( defined( $xml_data )) {
            my $remote_addr = parse_xml_data( $xml_data );
            return $remote_addr;
        } else {
            return -1;
        }
    }
    
    my $remote_addr = get_remote_addr();
    unless ( $remote_addr eq -1 ) {
        my $dsn = "dbi:mysql:$db_name:$db_host";
        my $dbh = DBI->connect($dsn, $db_user, $db_pass);
        my ($id) = $dbh->selectrow_array("SELECT `id` FROM `remote_addr` WHERE `remote_addr` = INET_ATON( '$remote_addr' ) AND `id` IN ( SELECT MAX(`id`) FROM `remote_addr`);");
        if ( defined($id) and $id gt 0 ) {
            $dbh->do("UPDATE `remote_addr` SET `date_end` = NOW() WHERE `id`=$id;");
        } else {
            $dbh->do("INSERT INTO `remote_addr` ( `remote_addr`, `date_start`, `date_end` ) VALUES ( INET_ATON( '$remote_addr'), NOW(), NOW() );");
        }
        $dbh->disconnect;
    }
    

Die Funktionsweise ist recht simpel. Die IP-Adresse wird mit der des letzten Eintrags in der Tabelle verglichen. Hat sie sich nicht geändert, wird das Enddatum mit der aktuellen Zeit aktualisiert. Ist es eine andere Adresse, wird ein neuer Eintrag eingefügt.  
Jetzt muß das Skript nur noch regelmäßig aufgerufen werden. Sinnvollerweise erledigt das cron.

`*/1 * * * * /usr/local/bin/remote_addr_logger`

Die Einträge können wie folgt aus der Datenbank ausgelesen werden:

    SELECT INET_NTOA(`remote_addr`), `date_start`, `date_end` FROM `remote_addr`;
    

Und jetzt bin ich mal gespannt…