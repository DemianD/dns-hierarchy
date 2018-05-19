# Configuratie van DNS servers onder Linux

De figuur in bijlage stelt een intranet bestaand uit een aantal Linux computers voor, met corresponderend IP-adres, van de vorm 192.168.16.z . Het getal z lees je af links van de naam van de computer. De getallen rechts van de naam van de computer moet je negeren. De computers staan gegroepeerd in een tabel met als header de naam van het domein waarin ze zich bevinden. De rechthoeken die domeinen groeperen stellen dan weer een zone voor. Stippellijnen duiden op een domein/sub­domein relatie. De pijlen laten toe om de primaire name­server van elke zone te achterhalen. Je hoeft geen reverse DNS te configureren.

### Vraag

Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers, waarbij je er rekening moet mee houden dat elk van deze servers ook secundaire nameserver is voor alle zones van de andere server: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht!

### Oplossing
Vertrekkend van de oplossing die zich in de map hierboven bevindt, heb ik de extra configuratiestappen hier besproken.

### Te vragen
Wat ik niet zeker weet:

Stel, Nielsen heeft volgende zone:

```
zone "." IN {
        type hint;
        file "named.ca";
};
```

Vivaldi is de root server. Hoe kunnen we dan de zone van Vivaldi bij Nielsen als slave instellen?
Want stel het onderste, dan heb je twee keer zone ".".

```
zone "." IN {
        type hint;
        file "named.ca";
};

zone "." IN {
        type slave;

        masters {
            192.168.16.155; // Vivaldi
        };

        file "slaves/root";
};
``` 

Ik zou misschien veronderstellen dat het eerste zone object weg mak.
Maar dit weet ik niet zeker.
