# INGInious
## Instalación

Repositorio: https://github.com/UCL-INGI/INGInious

Documentación: http://inginious.readthedocs.org/ 

>Se siguió la documentación lo más fielmente posible, aunque hubo ciertos elementos:
>- El servidor web incluido no pudo usarse, a pesar de que parecía funcionar bien, por que se instaló en un servidor headless remoto. La configuración por defecto del servidor web permitía solo accesos por localhost, y al tratar de cambiar para acceder desde fuera, hubo problemas que no se logró resolver. Se decidió entonces usar Apache, con el cual el acceso externo funcionó correctamente.
>- La instalación de paquetes con pip se hizo siempre como root: `sudo pip3 install ...`. Esto provocó que la ubicación de ejecutables y librerías fuera diferente a la de la documentación, básicamente `/usr/local/bin...` en lugar de `/usr/bin...` (entre otros directorios)
>- Para el uso de `mod_wsgi` en Apache se usó `pip3.6`, en lugar de `pip3.5`, lo que tambié ocasionó que algunos archivos y directorios cambien, básicamente `...3.6...` en lugar de `...3.5...`
>- ...

A continuación se retoma o copia solo las partes específicas del proceso utilizado:

### OS

Linux (Centos 7.9)

> Atención: si no se hace antes de ejecutar los comandos bash un `sudo su`, hay que anteponer a todos los comandos `sudo`

### Prerequisitos y dependencias

```bash
yum install -y epel-release
yum install -y git gcc libtidy python3 python3-devel python3-pip zeromq-devel yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce docker-ce-cli
cat <<EOF > /etc/yum.repos.d/mongodb-org-4.4.repo
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/\$releasever/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
EOF
yum install -y mongodb-org mongodb-org-server
```

### Iniciar servicios

```bash
systemctl start mongod
systemctl enable mongod
systemctl start docker
systemctl enable docker
```

### Instalar INGInious

```bash
sudo pip3 install --upgrade git+https://github.com/UCL-INGI/INGInious.git@inginious-0.7
```

> Aparentemente este mismo comando también actualiza el software

### Configurar la instalación

```bash
mkdir -p /var/www/html/inginious
cd /var/www/html/inginious
mkdir tasks
mkdir backup
inginious-install
```

El último comando desencadena la utilidad de configuración. Se debe seguir el paso a paso.

> Cuando se solicite indicar directorios, es necesario poner el path absoluto de los mismos

Entre otras cosas, esto genera el archivo `/var/www/html/inginious/configuration.yaml`, con el contenido (que variará dependiendo de lo que se responda):

```yaml
backend: local
backup_directory: /var/www/html/inginious/backup
local-config:
    debug_host: ocda.daoc.ml
mongo_opt:
    database: INGInious
    host: localhost
plugins: []
session_parameters:
    ignore_change_ip: false
    secret_key: <<aquí va un hash>>
    secure: false
    timeout: 86400
superadmins:
- superadmin
tasks_directory: /var/www/html/inginious/tasks
use_minified_js: false
```

> Este archivo se modifica un poco luego, al configurar Apache

### Instalar y configurar Apache

```bash
yum install httpd httpd-devel
pip3.6 install mod_wsgi

usermod -aG docker apache
usermod -aG mongod apache

chown -R apache:apache /var/www/html/inginious

cat  << EOF >> /etc/sysconfig/httpd
INGINIOUS_WEBAPP_CONFIG="/var/www/html/inginious/configuration.yaml"
INGINIOUS_WEBAPP_HOST="0.0.0.0"
INGINIOUS_WEBAPP_PORT="80"
EOF
```

Añadir al final de `/var/www/html/inginious/configuration.yaml`:

```yaml
local-config:
    tmp_dir: /var/www/html/inginious/agent_tmp
```

Crear el archivo `/etc/httpd/conf.d/inginious.conf` con la siguiente configuración:

```xml
<VirtualHost *:80>
    ServerName ocda.daoc.ml
    LoadModule wsgi_module /usr/local/lib64/python3.6/site-packages/mod_wsgi/server/mod_wsgi-py36.cpython-36m-x86_64-linux-gnu.so

    WSGIScriptAlias / "/usr/local/bin/inginious-webapp"
    WSGIScriptReloading On

    Alias /static /usr/local/lib/python3.6/site-packages/inginious/frontend/static

    <Directory "/usr/local/bin">
        <Files "inginious-webapp">
            Require all granted
        </Files>
    </Directory>

    <DirectoryMatch "/usr/local/lib/python3.6/site-packages/inginious/frontend/static">
        Require all granted
    </DirectoryMatch>
</VirtualHost>

<VirtualHost *:8080>
    ServerName my_inginious_domain
    LoadModule wsgi_module /usr/local/lib64/python3.6/site-packages/mod_wsgi/server/mod_wsgi-py36.cpython-36m-x86_64-linux-gnu.so

    WSGIScriptAlias / "/usr/local/bin/inginious-webdav"
    WSGIScriptReloading On

    <Directory "/usr/local/bin">
        <Files "inginious-webdav">
            Require all granted
        </Files>
    </Directory>
</VirtualHost>
```

> **IMPORTANTE:** para que WebDav funcione es muy probable que se deba verificar en el archivo `/etc/httpd/conf/httpd.conf`, que el servidor escuche en el puerto 8080, ya que normalmente estará escuchando solo en el 80. Para esto añada `Listen 8080` bajo `Listen 80`. 

Finalmente reiniciar Apache

```bash
sudo systemctl restart httpd
```

