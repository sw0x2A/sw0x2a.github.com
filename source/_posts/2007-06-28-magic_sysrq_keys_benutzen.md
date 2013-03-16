---
title: Magic SysRq keys benutzen
author: sw
layout: post
permalink: /2007/06/magic_sysrq_keys_benutzen/
categories:
  - Uncategorized
tags:
  - kernel
  - linux
  - magic sysrq
  - out-of-band
  - remoteconsole
  - serielle konsole
  - server
  - strato
  - sysrq
---
# 

Wenn ein Linux-System nicht mehr reagiert, ist der Reset-Knopf nur die letzte, aber nicht die einzige Option. Sollte man noch Zugang zum betroffenen System haben, können über das im Linux-Kernel integrierte sysrq-Interface Befehle übergeben werden.

Der Kernel muss mit Unterstützung für sysrq (CONFIG\_MAGIC\_SYSRQ) übersetzt worden sein. Im `/proc`-Dateisystem existiert dann der Eintrag sysrq-trigger.

Wenn Sie Zugriff auf die Console des Systems haben, betätigen Sie die Tastenkombination \[Alt]-[Druck\] ([S-Abf]). Danach können Sie mit einer weiteren Taste den gewünschten Befehl ausführen.  
Es folgt eine der Kernel-Dokumentation entnommenen Liste der unterstützten Befehle.

*   ‘r’ – Turns off keyboard raw mode and sets it to XLATE.
*   ‘k’ – Secure Access Key (SAK) Kills all programs on the current virtual console. NOTE: See important comments below in SAK section.
*   ‘b’ – Will immediately reboot the system without syncing or unmounting your disks.
*   ‘c’ – Will perform a kexec reboot in order to take a crashdump.
*   ‘d’ – Shows all locks that are held.
*   ‘o’ – Will shut your system off (if configured and supported).
*   ‘s’ – Will attempt to sync all mounted filesystems.
*   ‘u’ – Will attempt to remount all mounted filesystems read-only.
*   ‘p’ – Will dump the current registers and flags to your console.
*   ‘t’ – Will dump a list of current tasks and their information to your console.
*   ‘m’ – Will dump current memory info to your console.
*   ‘n’ – Used to make RT tasks nice-able.
*   ‘v’ – Dumps Voyager SMP processor info to your console.
*   ‘w’ – Dumps tasks that are in uninterruptable (blocked) state.
*   ‘x’ – Used by xmon interface on ppc/powerpc platforms.
*   ’0′-’9′ – Sets the console log level, controlling which kernel messages will be printed to your console. (’0′, for example would make it so that only emergency messages like PANICs or OOPSes would make it to your console.)
*   ‘f’ – Will call oom_kill to kill a memory hog process.
*   ‘e’ – Send a SIGTERM to all processes, except for init.
*   ‘g’ – Used by kgdb on ppc platforms.
*   ‘i’ – Send a SIGKILL to all processes, except for init.
*   ‘h’ – Will display help (actually any other key than those listed above will display help. but ‘h’ is easy to remember :-)

Beim Zugriff aus der Ferne senden Sie als Benutzer root die Befehle an /proc/sysrq-trigger. Mit `echo s > /proc/sysrq-trigger` weisen Sie Linux an, seine Puffer auf die Festplatten zurückzuschreiben (“sync”). Wenig später können mit [e] die laufenden Prozesse beendet werden (“SIGTERM”); Prozesse, die diesem Befehl nicht Folge leisten werden mit [i] erledigt (“SIGKILL”). Danach können Sie mit `echo u > /proc/sysrq-trigger` alle Dateisysteme read-only remounten und mit `echo b > /proc/sysrq-trigger` einen Reset auslösen.

Wer eine serielle RemoteConsole an einem entfernten Server nutzt, kann mitunter über spezielle Tastenkombinationen Befehle an sysrq zu übergeben. Bei der RemoteConsole von STRATO-Servern ist über die Tastenfolge [~]-[b]-[r]-[e]-[a]-[k] und einer weiteren Taste ein Befehl ausführen.

Damit die sysctl-Schnittstelle nicht mißbraucht wird, gibt es einen Schutzmechanismus: Erst wenn in `/proc/sys/kernel/sysrq` ein Wert größer als 0 eingetragen ist, reagiert das System überhaupt auf den Tastendruck oder die Ausgaben nach `/proc/sysrq-trigger`. Gegebenenfalls müssen Sie die Funktion erst mit `echo 1 > /proc/sys/kernel/sysrq` einschalten.