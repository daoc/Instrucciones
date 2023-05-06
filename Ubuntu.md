# Linux

Estas instrucciones funcionan para Ubuntu en general

## Cambiar hostname permanentemente

- Edite el archivo */etc/hostname* `sudo nano /etc/hostname`, ponga el nuevo nombre, grabe con `Ctrl+o` y cierre con `Ctrl+x`
- Edite el archivo */etc/hosts* `sudo nano /etc/hosts`, cambie el nombre en la segunda línea (como lo hizo en la isntrucción anterior), grabe con `Ctrl+o` y cierre con `Ctrl+x`
- Resetee `sudo reboot`

## Renovar IP

- Verifique el nombre de la interfaz sobre la cual desea renovar la IP con `ifconfig`
- Suponiendo que la interfaz de interés es, por ejemplo: `enp0s8`
  - Libere la IP con: `sudo dhclient -v -r enp0s8`
  - Obtenga nueva IP con: `sudo dhclient -v enp0s8`

## HTTPS en Ubuntu 18

Para un certificado autogenerado:
Revise: https://hostadvice.com/how-to/configure-apache-with-tls-ssl-certificate-on-ubuntu-18/

Para tener un certificado emitido por una autoridad verdadera puede:
- Obtenga un dominio en Freenom
- Obtenga el certificado con Let's Encrypt
- Y si no pudo registrarlo automáticamente en su server, configure con las instrucciones para el autogenerado

## Mantener la laptop funcionando al cerrar la tapa

Abrir el archivo de configuración para editarlo:

`$ sudo nano /etc/systemd/logind.conf`

Buscar `HandleLidSwitch` y ponerlo, descomentado, así: `HandleLidSwitch=ignore`

Reiniciar el servicio logind:

`$ sudo service systemd-logind restart`

## Reducir el tamaño de una partición lógica en LVM

Ref: https://askubuntu.com/questions/124465/how-do-i-shrink-the-root-logical-volume-lv-on-lvm

Esto me tocó hacer para luego poder aumentar el tamaño del swap

Arrancar con un USB que tenga Ubuntu

`$ sudo lvs` //esto es solo para saber los nombres de las particiones

`$ sudo lvreduce --resizefs --size -8G /dev/ubuntu-vg/root`


## Aumentar el tamaño del swap en lvm

Ref: https://askubuntu.com/questions/226520/how-can-i-modify-the-size-of-swap-with-lvm-partitions

`$ sudo lvs` //esto es solo para saber los nombres de las particiones

`$ sudo swapoff /dev/ubuntu-vg/swap_1`

`$ sudo lvresize -L+8G /dev/ubuntu-vg/swap_1`

`$ sudo mkswap /dev/ubuntu-vg/swap_1`

`$ sudo swapon /dev/ubuntu-vg/swap_1`

## Crear Hotspot con WiFi

Ref: https://unix.stackexchange.com/a/384513

`nmcli dev wifi hotspot ifname wlp9s0 con-name Hotspot ssid griinf_iot password "test1234"`

La conexión se crea con el nombre Hotspot. Para editarla, el archivo se encuentra en `/etc/NetworkManger/system-connections/Hotspot`. Especialmente puede ser necesario incluir el dns local:

`[ipv4]
dns=10.42.0.1;8.8.8.8;8.8.4.4;`

## Remover cloud-init (demoras en arranque)

Ref: https://makandracards.com/operations/42688-how-to-remove-cloud-init-from-ubuntu

`echo 'datasource_list: [ None ]' | sudo -s tee /etc/cloud/cloud.cfg.d/90_dpkg.cfg`

`sudo apt purge cloud-init`

`sudo rm -rf /etc/cloud/; sudo rm -rf /var/lib/cloud/`

`sudo reboot`

## Instalar mDNS (avahi)

`sudo apt install avahi-daemon`

Habilitar en arranque automàtico:

`sudo systemctl enable avahi-daemon`

