# Setting up a Raspberry Pi as an access point: WiFi hotspot

`https://www.pcgamer.com/how-to-build-a-raspberry-pi-wireless-access-point/`

Respaldo en el archivo:
[Raspberry Pi as wifi hotspot.pdf](Raspberry Pi as wifi hotspot.pdf)

Con lo indicado anteriormente ya tenemos el DNS con dnsmasq (no hace falta BIND). Para registrar hostnames basta con añadirlos a /etc/hosts. Ver:

`https://wiki.debian.org/HowTo/dnsmasq`

Para activar correctamente la resolución del DNS es necesario crear el archivo `/etc/resolv.conf.head` y añadir: `nameserver 127.0.0.1`
