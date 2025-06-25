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

### Dato importante
Para estar en escucha por el puerto 443, tenemos que estar como root o usuario privilegiado, de lo contrario nos va a aparecer este error

![[nc -nlvp1.png]]
Como usuario no privilegiado, podemos estar en escucha por el puerto 1024 en adelante, si queremos un puerto menor a este tenemos que estar logeados como root o usuario privilegiado