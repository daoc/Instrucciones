# Proxmox: clonar VM (para tener una nueva IP)
Luego de haber clonado la VM es necesario:

- cambiar nombre en /etc/hostname
 
`$ sudo nano /etc/hostname`

- cambiar nombre en /etc/hosts
 
`$ sudo nano /etc/hosts` (se encuentra junto a 127.0.1.1)

- regenerar machine-id

```
$ sudo rm /etc/machine-id
$ sudo dbus-uuidgen --ensure=/etc/machine-id
$ sudo rm /var/lib/dbus/machine-id
$ sudo dbus-uuidgen --ensure
$ sudo reboot
```

# VirtualBox: clonar VM (para tener una nueva IP)

REF: https://superuser.com/questions/1470219/virtualbox-dhcp-server-is-leasing-the-same-ip-to-all-guest-vms-connected-to-a-v

Esto es necesario hacer antes de clonar la máquina original:

```
sudo rm /etc/machine-id /var/lib/dbus/machine-id
sudo touch /etc/machine-id
sudo chmod 444 /etc/machine-id
sudo shutdown -h now
```
Ahora, no vuelva a abrir la máquina y clónela directamente (si la vuelve a abrir se regenera machine-id y hay que repetir esto)

Los nuevos clones regenerarán machine-id con un dato diferente.

# KVM Crear bridge network

Esto permite que las VM puedan ser accedidas desde otras máquinas en la red (menos el host, para eso ver siguiente punto)

Instalar: `sudo apt-get install bridge-utils`

Editar: `sudo nano /etc/network/interfaces` y añadir:
```
auto br0

iface br0 inet dhcp
    bridge_ports enp0s3
```
(verificar el nombre de la interfaz host externa. En este caso es enp0s3)

Reiniciar el host: `sudo reboot`

Añadir a VirtManager para que esté disponible en las nuevas VM que se creen

Cree un archivo bridge.xml en el home del host:

```
:$ cd
:$ nano bridge.xml
```
Añada:

```
<network>
  <name>bridge</name>
  <forward mode="bridge"/>
  <bridge name="br0"/>
</network>
```
Active y verifique:

```
:$ virsh net-define bridge.xml
:$ virsh net-autostart bridge
:$ virsh net-start bridge
:$ virsh net-list --all
```


# Con Multipass no se genera una red interna (Win 10 Home)

En Ubuntu todo funciona bien automáticamente. Parece que el problema es solo cuando hay que usar VirtualBox, lo cual es necesario en Win 10 Home.

Para resolver es necesario crear a mano la interfaz Host-only (o bridged). En el link las instrucciones de base: https://jon.sprig.gs/blog/post/1574

(NOTA: no es una solución muy fácil de ejecutar que se diga pero funciona bien)

Es necesario descargarse primero el programa PsExec64.exe de esta página: https://docs.microsoft.com/en-us/sysinternals/downloads/psexec 

Este es el link directo al zip: https://download.sysinternals.com/files/PSTools.zip

Hay que descomprimir en algún directorio, por ejemplo: C:\Users\ordon\Tools\PSTools

### Crear nueva VM Multipass:

NOTA: estamos cargando en la nueva VM, docker y el jdk. Esto es optativo, claro.

Supongamos que el nombre va a ser: mp3

- multipass launch -n mp3
- multipass shell mp3
- sudo apt update
- sudo apt upgrade
- sudo apt install avahi-daemon
- sudo apt install default-jdk
- curl -fsSL https://get.docker.com -o get-docker.sh
- sh get-docker.sh
- sudo usermod -aG docker ubuntu

-- salga y detenga la VM
- exit
- multipass stop mp3


-- Modificar la interfaz para poder trabajar entre todas las máquinas y el Host

-- Anotar el path al VBoxManage.exe entre comillas
- "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe"

-- Abrir terminal como Administrador
- Ir a Start, buscar el Terminal, darle click derecho, ir a More y dar click en Run as Administrator

-- ubicar el directorio donde tengan el PsExec64
- cd C:\Users\ordon\Tools\PSTools

-- buscar el nombre de la red, de preferencia: "VirtualBox Host-Only Ethernet Adapter" (en todo caso la misma para todas)
- PsExec64.exe -s "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list hostonlyifs

-- modificar la VM para asignarle esta red
- PsExec64.exe -s "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" modifyvm mp3 --nic2 hostonly --hostonlyadapter2 "VirtualBox Host-Only Ethernet Adapter"

-- Modificar el archivo de las interfaces en la máquina virtual para registrar el cambio
- multipass start mp3
- multipass shell mp3
- cd /etc/netplan

-- abra para edición el archivo que está ahí. Haga un ls para saber el nombre, generalmente: 50-cloud-init.yaml
- sudo nano 50-cloud-init.yaml

-- hay que aumentar:
```
        enp0s8:
            dhcp4: true
```

-- El archivo debe quedar así:
```
network:
    ethernets:
        enp0s3:
            dhcp4: true
            match:
                macaddress: 08:00:27:e0:fb:a2
            set-name: enp0s3
        enp0s8:
            dhcp4: true
    version: 2
```
-- No use tabs, solo espacios !

-- grabe y cierre con: Ctrl+O y luego Ctrl+X
- sudo netplan apply

-- y debería estar listo !
