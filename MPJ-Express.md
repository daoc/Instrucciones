# Configuración en cluster

1) Es necesario que se pueda hacer ssh sin password a todas las máquinas en el cluster
2) Es necesario que el /etc/hosts de cada máquina incluya la IP y el nombre de cada máquina, incluida la local!
3) Es necesario que antes de hacer el `mpjboot machines` por primera vez, se haya hecho un ssh a todas las máquinas, incluida la local!
4) En el archivo machines, el nombre de la máquina local debe estar al último. Si está primero, NO funciona!
