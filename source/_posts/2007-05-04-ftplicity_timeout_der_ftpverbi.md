---
title: 'ftplicity: Timeout der FTP-Verbindung'
author: sw
layout: post
permalink: /2007/05/ftplicity_timeout_der_ftpverbi/
categories:
  - Uncategorized
tags:
  - backup
  - duplicity
  - ftp
  - ftplicity
  - patch
  - python
  - strato
  - timeout
---
# 

Als Backup-Lösung wird auf einem meiner Server [ftplicity][1] (duplicity) eingesetzt. Seit ein paar Wochen kam es beim Erstellen der inkrementellen Backups gelegentlich zu Abbrüchen mit Fehlermeldungen wie der folgenden.

 [1]: http://www.heise.de/ct/06/13/links/216.shtml

    Traceback (most recent call last):
    File "/usr/bin/duplicity", line 373, in ?
    if __name__ == "__main__": main()
    File "/usr/bin/duplicity", line 367, in main
    else: incremental_backup(sig_chain)
    File "/usr/bin/duplicity", line 170, in incremental_backup
    bytes_written = write_multivol("inc", tarblock_iter, globals.backend)
    File "/usr/bin/duplicity", line 89, in write_multivol
    backend.put(tdp, dest_filename)
    File "/usr/lib/python2.4/site-packages/duplicity/backends.py", line 349, in put
    self.error_wrap('storbinary', "STOR " remote_filename, source_file)
    File "/usr/lib/python2.4/site-packages/duplicity/backends.py", line 335, in error_wrap
    except ftplib.all_errors, e: raise BackendException(e)
    duplicity.backends.BackendException

Um eine Ursache für die Fehlermeldung zu finden, erstellte ich mit tcpdump einen Mitschnitt des Netzverkehrs zum Backupservers. Die Analyse ergab, dass die FTP-Verbindung getrennt wird, weil duplicity keine Daten mehr sendet. Nach einer Pause von fünf Minuten bricht der Server die Verbindung ab (timeout) und wenn der Client danach versucht, den Rest des Backups zu übertragen, kommt es zu oben genannter Fehlermeldung.

Eine Ursache für die eingelegte Pause von duplicity habe ich bisher nicht finden können. Bei meiner Suche stiess ich jedoch auf einen [Patch][2], der duplicity um die Funktion erweitert, eine abgebrochene FTP-Verbindung wiederherzustellen. Damit läuft das Backup, mit Pausen, aber ohne Fehler, wieder durch.

 [2]: http://serversupportforum.de/forum/perl-php-python-bash/9463-problem-mit-duplicity-ftplicity-3.html#post104368