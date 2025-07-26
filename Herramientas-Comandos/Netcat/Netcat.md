#nc 
Netcat (o `nc`) es una herramienta de red que permite enviar y recibir datos a través de conexiones TCP o UDP.  
Se usa para crear [[Reverse shell]], transferir archivos, o escanear puertos.  
Es muy versátil y está presente en muchas distribuciones Linux y Windows.

-------
# Descargar archivo para mandar una [[Reverse shell]]
[Link de netcat para 64bits](https://github.com/int0x33/nc.exe/blob/master/nc64.exe?source=post_page-----a2ddc3557403----------------------) 
Vamos a levantar un server de [[python3 -m http.server]] desde nuestra maquina, para que por wget lo descargue la maquina victima
Esto normalmente lo usamos para ponernos en escucha con  [[rlwrap nc -lvnp 443]] para la reverseshell

----------
# Como pasarnos archivos entre maquinas

#### Desde la maquina la cual tiene los archivos, nos ponemos en escucha en este caso por el puerto 5000

```shell
nc -nlvp Puerto < NombreDelArchivo
Ejemplo: nc -nlvp 5000 < KeePassDumpFull.dmp
```

![[Keeper17.png]]
*Parametros del comando nc*
`-nlvp` Es un rejunte de todos los parametros n l v p
	`n` Es para que no aplique resolucion DNS
	`l` Es listen, que es para escuchar conexiones entrantes
	`v` Es de verbose para que reporte mas resultados en consola
	`p` Es para poner el puerto
	`<` Es el input a entregar, que en este caso es un archivo



#### Desde la maquina que quiere recibir los archivos
```shell
nc Ip Puerto > NombreDelArchivo
Ejemplo: nc 10.10.11.227 5000 > KeePassDumpFull.dmp
```
Aclaracion: Despues de unos segundos hay que cortar la conexion con CTRL + C, por que si no queda activa

![[Keeper16.png]]
*Parametros del comando nc*
	`>` Es el archivo al cual queremos mandar el output


