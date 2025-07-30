#sudo #sudo-l #sudo-u

----------

`sudo` significa **"superuser do"** ("hacer como superusuario") y es una herramienta de Linux/Unix que te permite **ejecutar comandos con privilegios elevados**, generalmente como **root** u otro usuario.

-----
# sudo -l

El comando `sudo -l` (de **list**) sirve para ver **qué permisos `sudo` tenés como el usuario actual**.

## Ejemplo 1
![[Bashed9.png]]
Aca por ejemplo sabemos que podemos ejecutar cualquier comando como el usuario scriptmanager sin proporcionar contraseña

## Ejemplo 2

![[Base19.png]]
Podemos ejecutar el binario /usr/bin/find como root 

------
# sudo -u

Sirve para ejecutar un comando como **otro usuario** (que no sea root necesariamente).

### Ejemplo
Aca estamos ejecutando el binario /bin/bash como el usuario scriptmanager siendo www-data

```shell
sudo -u scriptmanager /bin/bash
```

![[Bashed10.png]]