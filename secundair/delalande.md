# Delalande - secundaire nameserver

Delalande is de secundaire nameserver voor:

* Vivaldi
* Nielsen

Daarnaast zullen Vivaldi en Nielsen de secundaire nameservers voor Delalande zijn.

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
          192.168.16.143; // nielsen
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

zone "orchestral.fr" IN {
        type master;
        file "orchestral.fr";
};

zone "dk" IN {
        type slave;
        masters {
            192.168.16.143; // Nielsen
            192.168.16.155; // Vivaldi
        };
        file "slaves/dk";
};

zone "fr" IN {
        type slave;
        masters {
            192.168.16.143; // Nielsen
            192.168.16.155; // Vivaldi
        };
        file "slaves/fr";
};

zone "opera.fr" IN {
        type slave;
        masters {
            192.168.16.143; // Nielsen
            192.168.16.155; // Vivaldi
        };
        file "slaves/opera.fr";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

## Controle

```
named-checkconf
named-checkzone -i local orchestral.fr /var/named/orchestral.fr
```

## Herstarten:

```
systemctl restart named.service
systemctl status named.service
```