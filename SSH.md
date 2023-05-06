# SSH

SSH, o Secure Shell, es un protocolo utilizado para conectarse de forma segura a sistemas remotos a través de una red. Se utiliza comúnmente para acceder y gestionar servidores remotos, pero también se puede utilizar para otros fines, como la transferencia segura de archivos entre sistemas. A continuación se explica cómo instalar y utilizar SSH en Ubuntu.

## Instalación de SSH

Ubuntu normalmente viene con un cliente SSH preinstalado, pero puede que necesite instalar el servidor SSH si quiere permitir el acceso remoto a su propia máquina.

Para instalar el servidor SSH en Ubuntu, siga estos pasos:

1. Abra una ventana de terminal tecleando `Ctrl+Alt+T` o haciendo clic en el icono Terminal del lanzador de Ubuntu.
2. Escriba el siguiente comando y oprima `Enter`: `sudo apt install openssh-server`.
3. Introduzca su contraseña sudo cuando se le solicite y oprima `Enter`.
4. Espere a que se complete la instalación.

Verifique la instalación con `sudo service ssh status` (debe aparecer como "active")

## Usando SSH

Una vez instalado SSH, puede utilizarlo para conectarse a sistemas remotos. Para utilizar SSH, siga estos pasos:

1. Abra una ventana de terminal pulsando `Ctrl+Alt+T` o haciendo clic en el icono Terminal del lanzador de Ubuntu.
2. Escriba el siguiente comando y oprima `Enter`: `ssh nombreusuario@nombrehost`.
   - Sustituya `nombredeusuario` por el nombre de usuario del sistema remoto al que desea conectarse.
   - Sustituya `hostname` por el nombre de host o dirección IP del sistema remoto al que quiere conectarse.
3. Introduzca la contraseña de la cuenta de usuario en el sistema remoto cuando se le solicite y pulse `Enter`.
4. Si la conexión se realiza correctamente, iniciará sesión en la interfaz de línea de comandos del sistema remoto.

Ahora puede utilizar la interfaz de línea de comandos en el sistema remoto como si estuviera físicamente presente en la máquina.

## Opciones adicionales de SSH

SSH tiene un número de opciones y banderas que puede usar para personalizar su conexión. Aquí hay algunas de uso común:

- `-p port`: Usa un número de puerto específico para la conexión SSH (por defecto es 22).
- `-i keyfile`: Utiliza un archivo de clave privada específico para la autenticación (útil para la autenticación basada en claves).
- `-X`: Activa el reenvío X11, que te permite ejecutar aplicaciones gráficas en el sistema remoto y que se muestren en tu máquina local.
- `-L [dirección_de_enlace:]puerto:host:puerto_de_host`: Configura el reenvío local de puertos. Esto le permite reenviar el tráfico de un puerto en su máquina local a un puerto en el sistema remoto.
- `-R [bind_address:]puerto:host:hostport`: Configura el reenvío remoto de puertos. Esto le permite reenviar tráfico desde un puerto en el sistema remoto a un puerto en su máquina local.

Para más información sobre estas y otras opciones de SSH, puede consultar las páginas de manual de SSH escribiendo `man ssh` en el terminal.

## Conectarse por ssh sin password

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

Es casi imprescindible que todos los equipos del cluster se hayan configurando para ssh y sudo sin password (ver los dos puntos anteriores) y si quiere usar nombres de máquinas en lugar de IPs avahi-daemon puede ser necesario.

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
