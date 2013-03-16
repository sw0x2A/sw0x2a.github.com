---
title: SSL-Zertifikate erstellen und mit Apache nutzen
author: sw
layout: post
permalink: /2007/08/sslzertifikate_erstellen_und_m/
categories:
  - Uncategorized
tags:
  - anleitung
  - apache2
  - ca
  - cacert
  - cert
  - certificate
  - csr
  - debian
  - howto
  - openssl
  - ssl
  - x.509
  - zertifikat
---
# 

In der Kommunikation zwischen Client und Server werden Zertifikate benötigt, wenn sichergestellt werden soll, dass sich die Parteien vertrauen können. Ein Zertifikat muss von einer Zertifizierungsstelle (Certification Authority, CA) signiert werden; die CA bürgt für die Echtheit des Zertifikats.

Es soll ein SSL-Zertifikat (X.509) erstellt werden. Unabhängig davon, ob das Zertifikat von einer externen CA oder selbst signiert werden soll, muss zuerst immer eine Bürgschaftsanfrage (certificate request, CSR) generiert werden.

    $ openssl req -new -nodes -keyout private\_key.pem -out private\_key.pem -days 365  
    Generating a 1024 bit RSA private key  
    …………………….   
    ……….   
    writing new private key to ‘private_key.pem’  
    —–  
    You are about to be asked to enter information that will be incorporated  
    into your certificate request.  
    What you are about to enter is what is called a Distinguished Name or a DN.  
    There are quite a few fields but you can leave some blank  
    For some fields there will be a default value,  
    If you enter ‘.’, the field will be left blank.  
    —–  
    Country Name (2 letter code) [AU]:DE  
    State or Province Name (full name) [Some-State]:Berlin  
    Locality Name (eg, city) []:Berlin  
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:  
    Organizational Unit Name (eg, section) []:  
    Common Name (eg, YOUR name) []:example.com  
    Email Address []:webmaster@example.com 
    Please enter the following ‘extra’ attributes  
    to be sent with your certificate request  
    A challenge password []:  
    An optional company name []:  

Der Befehl erstellt den privaten Schlüssel und das CSR in einer Datei. Auf die Angabe eines Passworts wurde aus praktischen Gründen verzichtet. Würde ein privater Schlüssel mit Passwort in einen Serverdienst eingebunden werden, müsste das Passwort bei jedem Start des Dienstes erneut eingegeben werden. Da der private Schlüssel somit ungeschützt ist, muss er unbedingt vor fremden Zugriffen geschützt werden.

Nun kann das CSR an die CA übergeben und signiert werden. Eine gemeinschaftsbetriebene, nicht-kommerzielle Zertifizierungsstelle, die kostenfrei X.509-Zertifikate für verschiedene Einsatzgebiete ausstellt ist [CAcert][1].

    —–BEGIN CERTIFICATE REQUEST—–  
    ICHBINUNGÜLTIG9tMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCu/ppznA8u  
    DD0UzHX/YQj5doqi2vWvGNhg849RJpbJ/ vlW/ZhkBTJ6M8ry/dzg1lp XK08kkK  
    BgNVBAcTBkJlcmxpbjEhMB8GA1UEChMYSW50ZXJuZXQgV2lkZ2l0cyBQdHkgTHRk  
    MRQwEgYDVQQDEwtleGFtcGxlLmNvbTEkMCIGCSqGSIb3DQEJARYVd2VibWFzdGVy  
    pLZMP3XDjvFkZW8F22Aq3ySBaLGagKHxvwIDAQABoAAwDQYJKoZIhvcNAQEFBQAD  
    Eob45fj1XE9AOqTAT8noFcdK1uixXsgPYfqNiQrVXfAunoiLUhYYFYXRc1qAI3dA  
    MIIBzzCCATgCAQAwgY4xCzAJBgNVBAYTAkRFMQ8wDQYDVQQIEwZCZXJsaW4xDzAN  
    gYEAn/HkOtZyMCLZRkhG1Prph/1hbLilyARVul s8Z/J TnAWw6fxcyDpXGDTfNj  
    FDj7WXcxZ pfPF9lzfz8mI3NLNr8BSx/ejM7qHAjcie RUkUQo2KToSFv2D9w6cq  
    2eZW3F9helt3qae8hXBfbSTORYozLRF8c ZQHKkCJJjoZUs=  
    —–END CERTIFICATE REQUEST—– 

Nach einer Identitätsprüfung wird die Zertifizierungsstelle ein signiertes, öffentliches Zertifikat zurückgeben, das zusammen mit dem privaten Schlüssel benutzt werden kann. Das Zertifikat kann in die oben genannte Datei kopiert werden.

Um das Zertifikat für eine sichere Verbindung mit HTTPS zu verwenden, muss es auf den Server kopiert und im Webserver eingebunden werden. Im Beispiel wird ein Apache2 auf einem Debian GNU/Linux verwendet.

    # cp private\_key\_and_certificate.pem /etc/apache2/ssl/example.com.pem  
    # chmod 600 /etc/apache2/ssl/example.com.pem 

Im Webserver wird das Zertifikat mit der Direktive SSLCertificateFile eingebunden.

    NameVirtualHost *:443  
      
    ServerAdmin webmaster@example.com  
    ServerName example.com  
    SSLEngine On  
    SSLCertificateFile /etc/apache2/ssl/example.com.pem

Damit sind alle Vorbereitungen beendet und die sichere Verbindung kann genutzt werden.

 [1]: https://www.cacert.org/
