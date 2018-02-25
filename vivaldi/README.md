# vivaldi

- hostname: vivaldi
- IP-adres: 192.168.16.155

Voer onderstaande commando's uit als root.

## Stap 1
/etc/resolv.conf
```
nameserver 0.0.0.0
```

Sommige distro's overschrijven automatisch dit bestand.
Om dit te voorkomen voer je onderstaand commando uit.
Dit heeft aan dat het bestand niet meer bewerkt kan worden.

```
chattr +i /etc/resolv.conf
```

## Stap 2
/etc/named.conf

```
options {
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
};

logging {
        channel default_file {
                syslog daemon;
                severity info;
                print-time yes;
                print-severity yes;
                print-category yes;
        };
        category default{ default_file; };
};

zone "." IN {
        type master;
        file "root";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

## Stap 3
/var/named/named.ca

Dit bestand bevat de root dns-servers.
Doordat we zelf de root zijn, hebben we dit bestand niet nodig.

We mogen dit bestand
- laten staan
- verwijderen
- bewerken

Dit zal geen impact hebben.

## Stap 4
/var/named/root

```
; zonebestand root
$TTL	60

@       IN      SOA     vivaldi. demian.ugent.be. (
                                2018022101         ; serial YYYYMMDDXX
                                60	   	   ; refresh
                                60		   ; retry
                                60		   ; expire
                                60 )               ; minimum
                
                NS	vivaldi.

vivaldi          IN  A   192.168.16.155

; dk afstoten
dk               IN  NS  nielsen.dk.

; fr afstoten
fr               IN  NS  nielsen.dk.

nielsen.dk.      IN  A   192.168.16.143

; een test a-record
test		 IN  A	192.168.16.101
```

## Stap 5
Controle:

```
named-checkconf
named-checkzone -i local . /var/named/root
```

## Stap 6
Herstarten:

```
systemctl restart named.service
```

## Stap 7
Start bind wanneer het systeem opstart:

```
systemctl enable named.service
```

## Stap 8
Controle:

```
dig axfr .
dig vivaldi.                   # moet 1 answer hebben
dig test.                      # moet 1 answer hebben
```
