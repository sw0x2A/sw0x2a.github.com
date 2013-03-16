---
title: Eine E-Mail mit Telnet und SMTP AUTH relayen
author: sw
layout: post
permalink: /2007/04/eine_email_mit_telnet_und_smtp/
categories:
  - Uncategorized
tags:
  - auth
  - base64
  - mail
  - perl
  - smtp
  - smtp auth
  - telnet
---
# 

Für das folgende Beispiel wird davon ausgegangen, dass der Mailserver PLAIN als Authentifizierungsmechanismus anbietet. Für SMTP AUTH müssen die Zugangsdaten als base64-kodierter String übergeben werden. Die Übersetzung generiert ein Perl-Einzeiler, wobei hier im Beispiel “username” als Benutzername und “password” als Passwort angegeben werden:

    $ perl -MMIME::Base64 -e ‘print encode_base64(“usernameusernamepassword”);’  
    dXNlcm5hbWUAdXNlcm5hbWUAcGFzc3dvcmQ= 

Mit dem ausgegebenen String kann nun die Authentifizierung durchgeführt werden.

    $ telnet mail.example.com 25  
    220 mail.example.com ESMTP Postfix  
    EHLO client.example.com  
    250-mail.example.com  
    250-PIPELINING  
    250-SIZE 10240000  
    250-VRFY  
    250-ETRN  
    250-STARTTLS  
    250-AUTH LOGIN PLAIN DIGEST-MD5 CRAM-MD5  
    250 8BITMIME  
    AUTH PLAIN dXNlcm5hbWUAdXNlcm5hbWUAcGFzc3dvcmQ=  
    235 Authentication successful  
    MAIL FROM:  
    250 Ok  
    RCPT TO:  
    250 Ok  
    DATA  
    354 End data with .  
    Some text  
    .  
    250 Ok: queued as 123456789A  
    QUIT  
    221 Bye
