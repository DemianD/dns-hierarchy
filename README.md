# DNS Hiërarchie

<p align="center">
    :construction: Work in Progress! :construction:
</p>

## Initialisatie
1. Configureer vivaldi
2. Configureer nielsen
3. Configureer delalande

## Controle
Kopieer het bestand `test/digA.sh` naar een van de DNS-servers.
Voer daarna volgend commando uit:

```
bash digA.sh | grep -A1 ANSWER | grep 192.168.16
```

Dit bevat een lijst van alle DNS-Servers die bereikbaar zijn. Indien niet alle Nameservers gevonden worden, dat klopt er nog iets niet.


## Schema
![Schema](./.img/schema.png#2)

## Debugging

### Poort

Het is zo dat DNS Servers met elkaar communiceren via het poortnummer `:53`.
Om te controleren of deze poort open is probeer je volgende commando uit vanaf een andere host:

```
nmap 192.168.16.??? -p 53
```

Het resultaat (state) moet `open` zijn.
Waarden als `closed` of `filtered` duiden een probleem aan.

**Controle**

Op de server waar de bind-instantie draait, voer je volgend commando uit:

```
netstat -lntp
```

Deze toont op welke poorten er geluisterd wordt.
Zorg dat er geluisterd wordt op poort `53`.

**Mogelijkse oplossing**

```
systemctl start named.service
iptables --flush
```
