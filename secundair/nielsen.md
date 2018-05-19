# Nielsen - secundaire nameserver

Nielsen is de secundaire nameserver voor:

* Vivaldi
* Delalande

Daarnaast zullen Vivaldi en Delalande de secundaire nameservers voor Nielsen zijn.

## Configuratiebestand

/etc/named.conf

```diff
options {
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";

        dnssec-enable no;
        dnssec-validation no;

        // als Primaire server moet je instellen wie uw zones kan transfereren (overnemen).
        allow-transfer {
            192.168.16.155; // vivaldi
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
        type hint;
        file "named.ca";
};

zone "dk" IN {
        type master;
        file "dk";
};

zone "fr" IN {
        type master;
        file "fr";
};

zone "opera.fr" IN {
        type master;
        file "opera.fr";
};

zone "orchestral.fr" IN {
        type slave;

        masters {
            192.168.16.128; // Delalande
            192.168.16.155; // Vivaldi
        };

        file "slaves/orchestral.fr";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

## Controle

```
named-checkconf
named-checkzone -i local dk /var/named/dk
named-checkzone -i local fr /var/named/fr
named-checkzone -i local opera.fr /var/named/opera.fr
```

## Herstarten:

```
systemctl restart named.service
systemctl status named.service
```