#sudo-l #abusobinarioscriptmanager

# Enumeracion

Con [[sudo]] usamos el comando `sudo -l`
```shell
sudo -l
```

----------
# Explotacion

Vemos que podemos correr el binario de scriptmanager sin usar contraseña
![[Bashed9.png]]

Ahora vamos a usar con [[sudo]] el comando ``sudo -u scriptmanager /bin/bash`` para ejecutar la bash como el usuario scriptmanager

![[Bashed10.png]]
Ahora estamos como scriptmanger