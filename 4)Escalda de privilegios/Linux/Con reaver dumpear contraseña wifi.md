#reaver #ifconfig #ps-faux 

-----

Vamos a listar que procesos estan corriendo con [[ps faux y ps -eo]] 
```shell
ps -faux
```
![[Wifinetic9.png]]
Vemos que esta corriendo el servicio [[hostapd]], asi que vamos a ver por que interfaz de red puede estar corriendo el servicio. Usamos [[ifconfig]] poniendo en consola `ifconfig`. Y vemos que nos parece sospechosa la red *mon0*

![[Wifinetic10.png]]
Con la herramienta [[reaver]] vamos a dumpear la contraseña

```
reaver -i mon0 -b 02-00-00-00-02-00-30-3A-00-00-00-00-00-00-00-00 -vv
```

![[Wifinetic11.png]]
Obtenemos la contraseña: *WhatIsRealAnDWhAtIsNot51121!*
Vemos si reutiliza la contraseña para el usuario *root*. Y pudimos entrar con la contraseña que sacamos previamente. Y ya podemos catear las flags
![[Wifinetic8.png]]