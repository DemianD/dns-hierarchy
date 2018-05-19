# Vivaldi - secundaire nameserver

Vivaldi is de secundaire nameserver voor:

* Nielsen
* Delalande

Daarnaast zullen Nielsen en Delalande de secundaire nameservers voor Vivaldi zijn.

## Configuratiebestand

/etc/named.conf

```diff
options {
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";

       // als Primaire server moet je instellen wie uw zones kan transfereren (overnemen).
       allow-transfer {
           192.168.16.143; // nielsen
           192.168.16.128; // delalande
       };

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

// Vivaldi is een slave voor de zone orchestral.fr
// Vivaldi haalt zijn gegevens van Delalande (192.168.16.128)
zone "orchestral.fr" IN {
        type slave;
        masters {
            192.168.16.128; // Delalande
            192.168.16.143; // Nielsen
        };
        file "slaves/orchestral.fr";
};

// Vivaldi is een slave voor de zone dk
// Vivaldi haalt deze gegevens van Nielsen (192.168.16.143)
zone "dk" IN {
        type slave;
        masters {
            192.168.16.143; // Nielsen
            192.168.16.128; // Delalande
        };
        file "slaves/dk";
};

zone "fr" IN {
        type slave;
        masters {
            192.168.16.143; // Nielsen
            192.168.16.128; // Delalande
        };
        file "slaves/fr";
};

zone "opera.fr" IN {
        type slave;
        masters {
            192.168.16.143; // Nielsen
            192.168.16.128; // Delalande
        };
        file "slaves/opera.fr";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

## Controle

```
named-checkconf
named-checkzone -i local . /var/named/root
```

## Herstarten:

```
systemctl restart named.service
systemctl status named.service
```

## Testen:
**Voorbeeld**: 
- Zet de DNS-server van Delalande uit
- Probeer de DNS-records op te halen vanuit nielsen.

Dit zal werken, aangezien Vivaldi nu ook deze records van Nielsen bevat.
