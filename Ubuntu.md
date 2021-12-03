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

## Conectarse por ssh sin password

Ref: http://www.linuxproblem.org/art_9.html

Al trabajar en cluster muchos programas tienen que conectarse con otras máquinas por ssh para tareas de administración. Estas conexiones tienen que hacerse en modo no supervisado, es decir, sin la participación del usuario, lo que implica que no se puede pedir el password.

Para estos casos, lo mejor es utilizar clave pública-privada: se registra la clave pública del usuario local, en la máquina remota, y al conectarse por ssh el sistema ya no pedirá el password.

Suponga entonces, que es el usuario *cerca*, en la máquina *local*, y que desea conectarse como el usuario *lejos*, en la máquina *remota*: cerca@local --> lejos@remota

1) ingrese en *local* como *cerca*, y genere un par de claves, pero **sin password!**: cerca@local:~$ `ssh-keygen -t rsa`

Verá algo así:
```
  Generating public/private rsa key pair.
  Enter file in which to save the key (/home/cerca/.ssh/id_rsa): 
  Created directory '/home/a/.ssh'.
  Enter passphrase (empty for no passphrase): 
  Enter same passphrase again: 
  Your identification has been saved in /home/cerca/.ssh/id_rsa.
  Your public key has been saved in /home/cerca/.ssh/id_rsa.pub.
  The key fingerprint is:
  3e:4f:05:79:3a:9f:96:7c:3b:ad:e9:58:37:bc:37:e4 cerca@local
```
2) La forma más sencilla es usando: `ssh-copy-id`. Este script copia la clave pública del usuario actual (cerca) desde la máquina en la que se encuentra (local), hacia el usuario y máquina que usted elija, en el caso de este ejemplos hacia el usuario lejos en la máquina remota:

`ssh-copy-id lejos@remota`

El sistema le pedirá la clave de lejos en remota para completar la operación. Luego podrá ingresar a remota, como el usuario lejos, sin poner clave: cerca@local:~$ `ssh lejos@remota`

Si por alguna razón no tiene el script ssh-copy-id, deberá ejecutar los siguientes pasos:

3) Usando ssh, cree el directorio */home/lejos/.ssh*, en la máquina *remota* (como el usuario *lejos*, claro): cerca@local:~$ `ssh lejos@remota mkdir -p .ssh` (si el directorio ya existe, no hay problema). En esta conexión todavía debe poner el password del usuario *lejos*

4) Finalmente, añada la clave pública de *cerca* en el archivo *authorized_keys*, del directorio */home/lejos/.ssh*, de la máquina *remota*. Por última vez deberá ingresar el password de *lejos@remota*: cerca@local:~$ `cat .ssh/id_rsa.pub | ssh lejos@remota 'cat >> .ssh/authorized_keys'`

5) Ahora ya podrá ingresar sin password a *remota*, como *lejos*: cerca@local:~$ `ssh lejos@remota`

## Ejecutar comandos sudo sin poner password

Modificar las opciones de sudo en /etc/sudoers:

`sudo visudo` (Sí, con esto se modifica el archivo sudoers!!!)

Ir a la última línea (Sí, tiene que ir al final del archivo) e incluir:
```
<username> ALL=(ALL) NOPASSWD: ALL
Defaults:<username>	!requiretty
```
(NOTAS: La segunda línea resulta útil al conectarse en remoto y en lugar de `<username>` poner su nombre de usuario)

## Ejecutar comandos en remoto y en paralelo con dsh

Es casi imprescindible que todos los equipos del cluster se hayan configurando para ssh y sudo sin password (ver los dos puntos anteriores) y si quiere usar nombres de máquinas en lugar de IPs avahi-daemon es necesario.

Instalar dsh:

`sudo apt install dsh`

Modificar en el archivo de configuración de dsh que puede estar en: "/usr/local/etc/dsh.conf" o "/etc/dsh/dsh.conf" (puede estar en otro directorio!)
```
remoteshell =ssh  
showmachinenames = 1
```
Incluir los nombres de todas las máquinas en el archivo de dsh "~/.dsh/machines.list" o "/etc/dsh/machines.list" (puede estar en otro directorio!), ej:
```
rpi0.local
rpi1.local
rpi2.local
```
Ejecutar los comandos, por ejemplo el comando uptime; la 'a' indica que se lo hará en todas las máquinas de machines.list y la 'c' que será en paralelo:

`dsh -ac "uptime"`
