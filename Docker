# Cambiar el directorio principal

(donde se guardan containers e imágenes)

refs:

https://stackoverflow.com/questions/30127580/docker-daemon-flags-ignored

https://stackoverflow.com/questions/32070113/how-do-i-change-the-default-docker-container-location

Si no existe el dir, entonces:

> mkdir /etc/systemd/system/docker.service.d

> sudo /etc/systemd/system/docker.service.d/private-registry.conf

Luego añadir al archivo (que debería estar vacío):
```
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd --data-root /mnt/Secundario/docker -H fd:// --containerd=/run/containerd/containerd.sock
```

Es /mnt/Secundario/docker el nuevo directorio principal. El resto debe quedar igual. El doble ExecStart= es NECESARIO
