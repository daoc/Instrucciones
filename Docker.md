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
