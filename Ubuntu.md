# Linux

## reducir el tamaño de una partición lógica en LVM

Ref: https://askubuntu.com/questions/124465/how-do-i-shrink-the-root-logical-volume-lv-on-lvm

Esto me tocó hacer para luego poder aumentar el tamaño del swap

Arrancar con un USB que tenga Ubuntu

`$ sudo lvs` //esto es solo para saber los nombres de las particiones

`$ sudo lvreduce --resizefs --size -8G /dev/ubuntu-vg/root`


## aumentar el tamaño del swap en lvm

Ref: https://askubuntu.com/questions/226520/how-can-i-modify-the-size-of-swap-with-lvm-partitions

`$ sudo lvs` //esto es solo para saber los nombres de las particiones

`$ sudo swapoff /dev/ubuntu-vg/swap_1`

`$ sudo lvresize -L+8G /dev/ubuntu-vg/swap_1`

`$ sudo mkswap /dev/ubuntu-vg/swap_1`

`$ sudo swapon /dev/ubuntu-vg/swap_1`
