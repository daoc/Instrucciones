# Setting up a Raspberry Pi as an access point: WiFi hotspot

`https://www.pcgamer.com/how-to-build-a-raspberry-pi-wireless-access-point/`

Respaldo en el archivo:
[Raspberry Pi as wifi hotspot.pdf](Raspberry Pi as wifi hotspot.pdf)

Con lo indicado anteriormente ya tenemos el DNS con dnsmasq (no hace falta BIND). Para registrar hostnames basta con añadirlos a /etc/hosts. Ver:

`https://wiki.debian.org/HowTo/dnsmasq`

Para activar correctamente la resolución del DNS es necesario crear el archivo `/etc/resolv.conf.head` y añadir: `nameserver 127.0.0.1`

# Pantalla Kuman 3.5
[https://trickiknow.com/raspberry-pi-3-complete-tutorial-2018-lets-get-started/]

Instalar y activar la TFT 3.5: 

```
cd
sudo rm -rf LCD-show 
git clone https://github.com/goodtft/LCD-show.git 
chmod -R 755 LCD-show 
cd LCD-show/
sudo ./LCD35-show
```

Volver a activar HDMI:

```
cd ~/LCD-show
sudo ./LCD-hdmi
```

Volver a activar TFT:

```
cd ~/LCD-show
sudo ./LCD35-show
```
