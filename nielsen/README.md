# nielsen

- hostname: nielsen
- IP-adres: 192.168.16.143

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

        dnssec-enable no;
        dnssec-validation no;
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
        type hint;
        file "named.ca";
};

zone "dk" IN {
        type master;
        file "dk";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

## Stap 3
/var/named/named.ca

```
.           60    NS   vivaldi.
vivaldi.    60    A    192.168.16.155
```

## Stap 4
/var/named/dk

```
$TTL	60
@       IN      SOA     nielsen.dk. demian.hogent.be. (
                                2018022101         ; serial YYYYMMDDXX
                                60	   	   ; refresh
                                60		   ; retry
                                60		   ; expire
                                60 )	   	   ; minimum

                NS	nielsen.dk.
nielsen	IN	A	192.168.16.143
```

## Stap 5
Controle:

```
named-checkconf
named-checkzone -i local dk /var/named/dk
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
dig axfr dk
dig vivaldi.                   # moet 1 answer hebben

host vivaldi.
host test.                     
```