# IP No cambia en clones o VMs importadas

REF: https://superuser.com/questions/1470219/virtualbox-dhcp-server-is-leasing-the-same-ip-to-all-guest-vms-connected-to-a-v

Remove /etc/machine-id and /var/lib/dbus/machine-id

Create an empty /etc/machine-id

Give /etc/machine-id proper permissions (this does not seem to be strictly necessary but that's how it was initially)

Shutdown the machine and never start it again or you will have to redo the steps above!

Or in code:

```
sudo rm /etc/machine-id /var/lib/dbus/machine-id
sudo touch /etc/machine-id
sudo chmod 444 /etc/machine-id
sudo shutdown -h now
```

Now the base-machine can safely be cloned, and each new machine will generate its own unique machine-id on first boot.

# Con Multipass no se genera una red interna (Win 10 Home)

Es necesario crear a mano la interfaz Host-only (o bridged). En el link las isntrucciones, pero es mucho más complejo de lo que debería:
https://jon.sprig.gs/blog/post/1574

En Ubuntu, sin embargo, todo funciona bien automáticamente. Parece que el problema es solo cuando hay que usar VirtualBox.

### Crear nueva VM Multipass:

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
