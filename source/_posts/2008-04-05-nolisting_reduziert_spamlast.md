---
title: Nolisting reduziert Spam-Last
author: sw
layout: post
permalink: /2008/04/nolisting_reduziert_spamlast/
categories:
  - Uncategorized
tags:
  - anti-spam
  - greylisting
  - mail
  - mailserver
  - nolisting
  - spam
---
# 

[Nolisting][1] ist eine dem [Greylisting][2] ähnliche Maßnahme zur [Spam][3]-Bekämpfung. Ihr Vorteil ist, dass sie keiner Softwareinstallation oder Konfigurationsänderung auf dem Mailserver bedarf. Da die meisten Spammer noch immer nach dem Fire-and-Forget-Prinzip vorgehen, also eine Mail einzuliefern versuchen und im Fehlerfall die Zustellung nicht wiederholen, können sie durch absichtlich herbeigeführte, temporäre Fehler ausgetrickst werden. Beim Nolisting ist dieser Fehler, dass der am höchsten priorisierte Mailserver (MX) grundsätzlich nicht auf SMTP antwortet, während der tatsächliche Mailserver auf Platz 2 bereit steht. Nach [RFC 2821][4] (Simple Mail Transfer Protocol) muss ein Client jeden MX der Reihe nach durchprobieren, wenn der jeweils vorherige Host nicht erreichbar war. Demnach — so die Annahme — sollten Spammer nach dem ersten Versuch auf dem inaktiven MX keinen weiteren Einlieferungsversuch unternehmen, RFC-konforme Mailserver aber den richten Mailserver finden und die Zahl der Verbindungen und somit die Last auf diesem sinken. In einem Test konnte ich mich davon überzeugen, wie sehr diese Annahme zutreffend ist.

 [1]: http://nolisting.org/
 [2]: http://projects.puremagic.com/greylisting/
 [3]: http://de.wikipedia.org/wiki/Spam
 [4]: http://www.ietf.org/rfc/rfc2821.txt

Auf einem von mir betrieben Mailserver, der bereits einige Spamschutz-Maßnahmen (darunter sehr erfolgreich Greylisting) implementiert hat, wurde Nolisting zusätzlich eingesetzt. Zu erwarten war ein Rückgang der Verbindungsversuche ingesamt, der Rejects und der durchs Greylisting abgewehrten Clients.

In der folgenden Statistik wurden die angenommenen und abgelehnten E-Mails sowie die durchs Greylisting geblockten Verbindungen normalisiert erfasst. Der Durchschnittswert entspricht 100%. Die tatsächlichen Werte wurden relativ zu diesem angegeben.

[![][6]][6]

 [6]: /assets/2008/04/nolisting.png

Es ist deutlich zu sehen, dass mit Beginn des Nolistings sowohl rejected als auch greylisted connections auf etwa 25% absinken und die Zahl der angenommenen E-Mails (mehr oder weniger) konstant bleibt. Daraus folgt, dass lediglich jede vierte Spam-Mail den Server überhaupt erreicht und der Server dadurch entsprechend entlastet wird. Eine Entlastung des Servers wird vom Monitoring bestätigt.

Das Ergebnis ist leider nicht allgemein verbindlich. Das Mailaufkommen auf dem Testsystem ist insgesamt gering und starken Schwankungen unterlegen. Zudem sorgten die Osterfeiertage für ein geändertes Nutzungsverhalten und möglicherweise wurde das gute Ergebnis durch ein generell geringeres Spam-Aufkommen (http://www.heise.de/newsticker/meldung/105737) während des Test-Zeitraums begünstigt.

Trotz dieser Meßungenauigkeit kann Nolisting zweifellos als erfolgreiche und praktisch kostenlose Maßnahme gegen Spam empfohlen werden. Es bleibt, wie beim Greylisting auch, abzuwarten, ob oder wann die Spammer Mailqueues einsetzen werden, die dieses Verfahren dann übergehen können.

Was ist mit false positives (Servern, die gewünschte E-Mails einliefern sollen, aber am Durchprobieren der Mailserver scheitern)? Während des Tests wurde mir kein false positive bekannt. Leider lässt sich auf der Empfängerseite kaum klären, ob ein solcher Fall eingetreten ist, weil keine Verbindung zum Mailserver hergestellt wird. (Allein eine Firewall-Regel o.ä. auf dem toten MX könnte hier Informationen sammeln.) Letztlich können nur Server betroffen sein, die den RFC nicht korrekt umgesetzt haben und die Verantwortung für die Zustellung einer E-Mail liegt beim Sender, also ist das ein [PAL][7].

 [7]: http://de.wikipedia.org/wiki/Problem_anderer_Leute
