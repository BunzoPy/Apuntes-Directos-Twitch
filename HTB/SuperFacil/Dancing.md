
1)¿Qué significa la sigla de 3 letras SMB? 
	Server Message Block (bloque de mensajes del servidor). Es un **protocolo de red** que permite el intercambio de archivos, impresoras, puertos serie y otros recursos entre computadoras en una red

2)¿En qué puerto funciona SMB? 
	445
	El servicio `microsoft-ds` en el puerto 445 indica que está activo SMB, usado para compartir archivos e impresoras en red.

3)¿Cuál es el nombre del servicio para el puerto 445 que apareció en nuestro escaneo Nmap? 
	microsoft-ds

4)¿Cuál es el 'flag' o 'switch' que podemos utilizar con la utilidad smbclient para 'listar' los recursos compartidos disponibles en Dancing? 
	-L

5)¿Cuántas acciones hay en Dancing? 
	4
	Usamos el comnado ``smbclient -L 10.129.96.14 -p 445 -N``
		*Parametros:*
		`-L` para listar los recursos compartidos
		`-p` Especificar el puerto
		`-N` No solicita contraseña

6)¿Cuál es el nombre del recurso compartido al que podemos acceder al final sin contraseña? 
	WorkShares
	Si un recurso en `smbclient` **no tiene `$`**, es **visible públicamente** en la red.  
	Si **tiene `$`**, es un **recurso oculto o administrativo**.
	![[Dancing5.png]]

7)¿Cuál es el comando que podemos utilizar dentro del intérprete de comandos SMB para descargar los archivos que encontremos? 
	get


---------
## [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
![[Dancing1.png]]

![[Dancing2.png]]

![[Dancing3.png]]

![[Dancing4.png]]
Por el ttl vemos que es una maquina windows y que tiene muchos puertos abiertos, a nosotros el que nos interesa es el 445.  El servicio `microsoft-ds` en el puerto 445 indica que está activo SMB, usado para compartir archivos e impresoras en red.

----------
## Intrusion con [[smbclient]]

![[Dancing5.png]]

Nos conectamos a la carpeta WorkShares
```shell
smbclient //10.129.96.14/WorkShares -N
```
Son carpetas las de  Amy.J y James.P
Y en la de James esta la flag
![[Dancing6.png]]