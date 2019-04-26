#Flash Arduino NINA-W102 firmware

Obtener software en: https://github.com/arduino/nina-fw

Seguir instrucciones de Readme (menos la última)

En lugar de flashear con esptool como dice la última instrucción seguir: https://github.com/arduino/nina-fw/issues/7#issuecomment-447933508

Ejecutar `make firmware` (que va a generar un binario grande llamado NINA_W102.bin)

Ejecutar `python /home/diego/esp/esp-idf/components/esptool_py/esptool/esptool.py --chip esp32 --port /dev/ttyACM0 --baud 115200 --before default_reset --after hard_reset write_flash -z --flash_mode dio --flash_freq 40m --flash_size detect 0x0 NINA_W102.bin` 

O más fácil:

Modificar el archivo sdkconfig:

en lugar de : 

`CONFIG_ESPTOOLPY_PORT="/dev/ttyUSB0"`

poner:

`CONFIG_ESPTOOLPY_PORT="/dev/ttyACM0"`

Y ejecutar: `make flash`
