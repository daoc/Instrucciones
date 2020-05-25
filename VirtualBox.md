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
