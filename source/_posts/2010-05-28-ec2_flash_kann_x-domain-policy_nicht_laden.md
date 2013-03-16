---
title: 'EC2: Flash kann X-Domain-Policy nicht laden'
author: sw
layout: post
permalink: /2010/05/ec2_flash_kann_x-domain-policy_nicht_laden/
categories:
  - Uncategorized
tags:
  - amazon
  - aws
  - ec2
  - nat
  - security groups
---
# 

Der Flash Player versucht die Crossdomain-Policy erst vom Port 843/tcp zu laden. Bekommt er dort keine Antwort, versucht er es auf dem Service-Port, den es zu kontaktieren gilt. Bei unseren Anwendung im Amazon EC2 konnten wir dabei ein Problem auf Windows-Systemen beobachten (Mac und Linux hatten keine Schwierigkeiten). Der Flash Player fragte auf 843 an, mehrfach, aber erst viel zu spät auf dem Port des Dienstes, der die Policy ausliefert. Der Flash Player hatte bereits vorher die Verbindung gekappt.

Die Ursache ist nicht ganz klar, scheint aber im NATing zu liegen, dass Amazon für die öffentlichen IPs betreibt. Hierbei wird die Anfrage auf Port 843 ausgebremst. Als wir den Port in der Security Group freigegeben haben (ohne dafür einen Service anzubieten), ging die Anfrage durch, wurde vom Server zurückgewiesen und der Flash Player fragte unsere Anwendung auf dem Service-Port wie gewünscht nach der Policy.