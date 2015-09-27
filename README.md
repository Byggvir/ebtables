# ebtables

Regeln um die Skype Nutzung zu verhindern.

Aufgrund der Belästigungen von durch einige viel und laut Telefonierern musste ich dieses Script leider entwickeln, da die Besitzer des Router sonst keien andere Alternative  hatten, als die Router abzuschalten. Dies ist zwar nicht im Sinne des Freifunk, aber lieber etwas weniger freien Funk als kein Freifunk.

Die Regeln verhindern die Anmeldung bei Skype. Keine Anmeldung => kein Telefoieren. Dies kann zwar von versierten Nutzern umgangen werden, dazu ist ein nicht alltägliches Know How erforderlich. 

Die Anmeldung bei Skype über ein eventuell auf den Router eingerichtetes privates WLAN ist weiterhin möglich.

## Datei 010-noskype

Enthält die ebtables Regeln, die die Anmeldung bei Skype aus dem WLAN "Freifunk" beim Start des Routers unterbinden.

Die Datei "010-noskype" muss in das Verzeichnis "/lib/gluon/ebtables/" kopiert werden, damit der Router die Regeln beim Start über /etc/init.d/gluon-ebtables lädt.

## Datei noskype

Enthält die ebtables Regeln als Shell-Script.
 

## Bestimmung der zu blockierenden IP-Adressen

Die DSN Server von Skype haben den Namen "dsn<nummer>.d.skype.net". Dabei ist <nummer> derzeit eine Zahl zwischen 0 und 16. Mit folgendem Befehl können die aktuellen IP-Adressen auf einem Linux Rechner mit bash bestimmt werden.

 for i in {0..16} ; do dig +short dsn$i.d.skype.net; done | grep '^[1-9]' | sort -u

Auf dem Router mit busybox werden die IP-Adressen mit folgendem Befehl bestimmt: 

 for i in `seq 0 16` ; do nslookup dsn${i}.d.skype.net 8.8.8.8 | grep '^Address' | grep -v '8.8.8.8' | sed 's#.* ##' | grep '^[1-9]' ; done | sort -u

Da dies derzeit eine Liste mit 114 IP-Adressen ergibt, habe ich komplette /24 Netze gesperrt, damit die Prüfungen des Router nicht zu lang dauern. 

 for i in {0..16} ; do dig +short dsn$i.d.skype.net; done | grep '^[1-9]' | sed 's#\.[0-9]*$#.0/24#' | sort -u

## Achtung

Durch die Blockierung der /24 Netze werden möglicherweise auch andere Dienste blockiert. 

