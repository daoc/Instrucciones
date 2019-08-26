# Cambiar el directorio principal

(donde se guardan containers e imágenes)

refs:

https://stackoverflow.com/questions/30127580/docker-daemon-flags-ignored

https://stackoverflow.com/questions/32070113/how-do-i-change-the-default-docker-container-location

Parar docker:

> `sudo service docker stop`

> `sudo systemctl daemon-reload`

Si no existe el dir para la configuración (no debería existir), entonces:

> `sudo mkdir /etc/systemd/system/docker.service.d`

> `sudo nano /etc/systemd/system/docker.service.d/private-registry.conf`

Luego añadir al archivo que acabamos de abrir (que debería estar vacío):
```
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd --data-root /mnt/Secundario/docker -H fd:// --containerd=/run/containerd/containerd.sock
```
Es /mnt/Secundario/docker el nuevo directorio principal. El resto debe quedar igual. El doble ExecStart= es NECESARIO

Crear el directorio para docker:

> `sudo mkdir /mnt/Secundario/docker`

Copiar todo el contenido del directorio original:

> `sudo rsync -aqxP /var/lib/docker/ /mnt/Secundario/docker/`

Volver a arrancar docker:

> `sudo service docker start`

Tal vez sea necesario: `sudo systemctl daemon-reload`

# Cluster con Docker (Swarm)

(Ref: https://www.dataquest.io/blog/install-and-configure-docker-swarm-on-ubuntu/)

Todos los equipos, físicos o virtuales, deben tener instalado Docker.

Asegurarse que, si está activo el firewall (en Ubuntu es ufw) estén abiertos los puertos respectivos:

```
sudo ufw allow 2376/tcp && sudo ufw allow 7946/udp && sudo ufw allow 7946/tcp && sudo ufw allow 2377/tcp && sudo ufw allow 4789/udp

sudo ufw reload && sudo ufw enable

sudo service docker restart
```
En el Manager: 

> `docker swarm init`

Copiar la instrucción que aparece con el token y ejecutarla en cada uno de los Workers:

Ej: > `docker swarm join --token SWMTKN-1-089drgj3qeoaosqzheti6wwnh7zdkzu3qxo7yhc5z6o2uf2b89-6zpu1zyurzilaq3tok3lwxc73 192.168.0.176:2377`

A partir de ahora todas las instrucciones se ejecutan en el Manager
