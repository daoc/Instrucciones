# Linux

Estas instrucciones funcionan para Ubuntu 16.04

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

First log in on A as user a and generate a pair of authentication keys. Do not enter a passphrase:

a@A:~> `ssh-keygen -t rsa`
Generating public/private rsa key pair.
Enter file in which to save the key (/home/a/.ssh/id_rsa): 
Created directory '/home/a/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/a/.ssh/id_rsa.
Your public key has been saved in /home/a/.ssh/id_rsa.pub.
The key fingerprint is:
3e:4f:05:79:3a:9f:96:7c:3b:ad:e9:58:37:bc:37:e4 a@A

Now use ssh to create a directory ~/.ssh as user b on B. (The directory may already exist, which is fine):

a@A:~> `ssh b@B mkdir -p .ssh`
b@B's password: 
Finally append a's new public key to b@B:.ssh/authorized_keys and enter b's password one last time:

a@A:~> `cat .ssh/id_rsa.pub | ssh b@B 'cat >> .ssh/authorized_keys'`
b@B's password: 
From now on you can log into B as b from A as a without password:

a@A:~> `ssh b@B`

## Ejecutar comandos sudo sin poner password

Modificar las opciones de sudo en /etc/sudoers:

`sudo visudo` (Sí, con esto se modifica el archivo sudoers!!!)

Ir a la última línea (Sí, tiene que ir al final del archivo) e incluir:
```
<username> ALL=(ALL:ALL) NOPASSWD: ALL
Defaults:<username>	!requiretty
```
(NOTA: La segunda línea resulta útil al conectarse en remoto)

