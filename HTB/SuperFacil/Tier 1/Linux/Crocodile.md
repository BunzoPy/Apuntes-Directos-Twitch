Aca vamos a ver [[ftp]] y intrusion a la maquina por panel de logeo

1)¿Qué switch de escaneo de Nmap emplea el uso de scripts por defecto durante un escaneo?
	-sC

2)¿Qué versión de servicio se encuentra ejecutándose en el puerto 21?
	vsftpd 3.0.3

3)¿Qué código FTP nos devuelve el mensaje «Acceso FTP anónimo permitido»?
	230

4)Después de conectarse al servidor FTP utilizando el cliente FTP, ¿qué nombre de usuario debemos proporcionar cuando se nos pida iniciar sesión de forma anónima?
	anonymous

5)Tras conectarnos al servidor FTP de forma anónima, ¿qué comando podemos utilizar para descargar los archivos que encontremos en el servidor FTP?
	get

6)¿Cuál es uno de los nombres de usuario que suenan con más privilegios en “allowed.userlist” que descargamos del servidor FTP?
	admin

7)¿Qué versión de Apache HTTP Server se está ejecutando en el host de destino?
	Apache httpd 2.4.41

8)¿Qué conmutador podemos utilizar con Gobuster para especificar que buscamos tipos de archivo concretos?
	-x

9)¿Qué archivo PHP podemos identificar con la fuerza bruta de directorio que proporcionará la oportunidad de autenticarse en el servicio web?
	login.php


-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]


![[Crocodile1.png]]

![[Crocodile2.png]]

![[Crocodile3.png]]

Por el ttl sabemos que es una maquina linux, y que estan corriendo los servicios
ftp por el puerto 80 con vulnerabilidad de logeo con usuario anonymous. Y en el puerto 80 un apache

-----------
# [[Whatweb-wappalyzer]]
Cuando intento ejecutar el whatweb se me traba la pc, me paso dos veces, no se por que y tampoco me deja usar el script de nmap http-enum

---------

# [[FFUF]]

```shell
ffuf -u http://10.129.188.242/FUZZ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -e .php
```
Enumeramos con ffuf archivos .php en especifico por que eso nos indicaba las preguntas de la maquina

![[Crocodile7.png]]

Nos da un directorio que nos llama la atencion login.php
![[Crocodile8.png]]
Ahora solo nos faltaria hacernos de unas credenciales para logearnos

# Intrusion a la maquina por [[ftp]] y panel de logeo apache

Ingresamos a la maquina con el comando `ftp 10.129.188.242` y nos descargamos los dos archivos existentes con el comando get `get allowed.userlist` y `get allowed.userlist.passwd`

![[Crocodile4.png]]

El contenido de estos dos archivos:
![[Crocodile5.png]]

Con este usuario admin y contraseña rKXM59ESxesUFHAd
Vamos a entrar al panel de logeo qwue vimos previamente cuando enumeramos con ffuf

![[Crocodile8.png]]

Ingresamos las credenciales que sacamos antes por ftp

![[Crocodile9.png]]

Y ya tenemos la flag de la maquina