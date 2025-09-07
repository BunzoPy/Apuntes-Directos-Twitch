#abusobinarioapport-cli

--------

```shell
sudo -l
```
![[Devvortex30.png]]
Nos indica que todos pueden usar el binario apport-cli sin proporcionar contraseña
Usamos el comando `apport-cli -v` para ver la version y vemos que es la *2.20.11*
![[Devvortex35.png]]

Viendo el [siguiente articulo sobre CVE-2023-1326 hecho por diego-tella](https://github.com/diego-tella/CVE-2023-1326-PoC) tenemos este CVE para la version 2.26.0, asi que como tenemos una version mas vieja, vamos a probar ejecutar lo mismo

Vemos que si ejecutamos el binario de la siguiente forma``sudo /usr/bin/apport-cli /bin/bash`

![[Devvortex31.png]]
Luego cuando nos pide que queremos hacer, ponemos la *v* que es para ver el reporte
Y vamos a poner *!/bin/bash* para que se nos abra una shell con los permisos de root
![[Devvortex32.png]]
Ya ganamos acceso como root y podemos catear las flags
![[Devvortex34.png]]
