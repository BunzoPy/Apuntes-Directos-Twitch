El comando nc es de Netcat y se usa par escuchar conexiones entrantes, por ejemplo para recibir una reverse shell
```shell
nc -nlvp Puerto
Ejemplo: nc -nlvp 443
```
*Parametros del comando nc*
`-nlvp` Es un rejunte de todos los parametros n l v p
	`n` Es para que no aplique resolucion DNS
	`l` Es listen, que es para escuchar conexiones entrantes
	`v` Es de verbose para que reporte mas resultados en consola
	`p` Es para poner el puerto