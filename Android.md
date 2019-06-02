Android

Ir a ubicación de Android SDK

cd ~/Android/Sdk/emulator

Listado de emuladores disponibles

./emulator -list-avds

Arrancar emulador modificable (necesario para cambiar el archivo hosts)

./emulator -avd <nombre_disponible> -writable-system

Listado de emuladores conectados

adb devices

Correr emulador como root

adb root

Descargar archivo hosts para modificarlo

adb -s <nombre_conectado> pull /system/etc/hosts

Montar sistema archivos modo escritura

adb -s emulator-5554 remount

Subir archivo hosts modificado

adb -s <nombre_conectado> push ./hosts /system/etc/hosts


