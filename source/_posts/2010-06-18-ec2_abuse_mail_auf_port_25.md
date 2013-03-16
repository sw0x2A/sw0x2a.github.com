---
title: 'EC2 Abuse: Mail auf Port 25'
author: sw
layout: post
permalink: /2010/06/ec2_abuse_mail_auf_port_25/
categories:
  - Uncategorized
---
# 

Von unseren Instanzen in EC2 werden viele Mails über einen Smarthost verschickt. Amazon limitiert die Anzahl der Mails von Instanzen auf Port 25 jedoch (aus verständlichen Gründen (Stichwort: Spam)) und empfiehlt den Einsatz von Elastic IPs und die [Anmeldung als Mailversender][1] bei ihnen. Für uns kommt das nicht in Frage, da wir ja unseren Smarthost nutzen. Also habe ich schnell Submission (Port 587/tcp) auf dem Smarthost aufgemacht und die EC2-Instanzen liefern nun dahin aus, was Amazon nicht mehr zu beanstanden hat.

 [1]: http://aws.amazon.com/contact-us/ec2-email-limit-request/