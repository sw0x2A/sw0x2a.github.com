---
title: Bcc pro Queue in OTRS
author: sw
layout: post
permalink: /2010/03/bcc_pro_queue_in_otrs/
categories:
  - Uncategorized
tags:
  - otrs
  - perl
---
# 

Die Antworten auf Tickets einer bestimmten Queue im [OTRS][1] sollen per Bcc an eine weitere Adresse gesendet werden. Das sieht OTRS bisher nicht vor; die einzige Option ist ein generelles Bcc für alle Queues.  
Um hier Abhilfe zu schaffen, habe ich `Kernel/Modules/AgentTicketCompose.pm` ein wenig erweitert. Etwa bei Zeile 400 findet sich folgende Methode, bei der die mit Bcc beginnende Zeile wie folgt geändert wird.

 [1]: http://otrs.org/

`-Bcc            => $GetParam{Bcc},
 Bcc            => $Ticket{QueueID} == 83 ? ('bcc@example.com') : $GetParam{Bcc},`

Die QueueID (im Beispiel 83) und die Zieladresse müssen selbstverständlich angepaßt werden.

    # send email
        my $ArticleID = $Self->{TicketObject}->ArticleSend(
            ArticleType    => 'email-external',
            SenderType     => 'agent',
            TicketID       => $Self->{TicketID},
            HistoryType    => 'SendAnswer',
            HistoryComment => "%%$Recipients",
            From           => $GetParam{From},
            To             => $GetParam{To},
            Cc             => $GetParam{Cc},
            # Dirty hack to Bcc (forward) answers of tickets from queue id=83.
            #Bcc           => $GetParam{Bcc},
            Bcc            => $Ticket{QueueID} == 83 ? ('bcc@example.com') : $GetParam{Bcc},
            Subject        => $GetParam{Subject},
            UserID         => $Self->{UserID},
            Body           => $GetParam{Body},
            InReplyTo      => $GetParam{InReplyTo},
            Charset        => $Self->{LayoutObject}->{UserCharset},
            Type           => 'text/plain',
            Attachment     => @AttachmentData,
            %ArticleParam,
        );
    

Mit ein bisschen mehr Aufwand ließe sich das auch auf mehrere Queues ausweiten sowie die Definition der QueueIDs und Bcc-Adressen in die Konfigurationsdatei auslagern. Das überlasse ich dem geneigten Leser als Hausaufgabe.