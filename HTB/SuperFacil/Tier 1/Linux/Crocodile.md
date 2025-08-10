---
title: Writeup crocodile - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - crocodile
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina crocodile en Hack The Box.
keywords:
  - writeup crocodile
  - hack the box crocodile
  - resolución máquina crocodile
  - crocodile hack the box
  - htb crocodile
---
---
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+1/Linux/Crocodile)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=tiEFT4doIOQ)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=yeiT-wREvZE)

---

#veryeasy #linux #nmap #ping #gobuster #ftp

------
# Guided mode

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
*TTL:* Maquina linux
*Puertos:*
	`21`[[ftp]]
	`80`HTTP
*Datos Importantes:* 
	En el servicio de [[ftp]] nos podemos conectar como el usuario *anonymous*

-----------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.129.1.15
```

![[Crocodile10.png]]
No nos da ninguna informacion relevante para esta maquina

---------
# Enumeracion con [[Gobuster]]

```shell
gobuster dir -u http://10.129.1.15 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64 -x php,bak,txt,html
```

![[Crocodile7.png]]

Nos da un directorio que nos llama la atencion login.php
![[Crocodile8.png]]

-------
# Robo de credenciales por [[ftp]]

Ingresamos a la maquina con el comando `ftp 10.129.188.242` con el usuario anonymous
Nos descargamos los dos archivos existentes con el comando get `get allowed.userlist` y `get allowed.userlist.passwd`

![[Crocodile4.png]]

Con [[cat]] usamos los comandos `cat allowed.userlist` `cat get allowed.userlist.passwd` para ver el contenido de estos dos archivos:
![[Crocodile5.png]]
Sacamos las credenciales
admin:rKXM59ESxesUFHAd

--------
# Intrusion con credenciales al panel de logeo

![[Crocodile8.png]]

Ingresamos las credenciales que sacamos antes por [[ftp]]

![[Crocodile9.png]]

Y ya tenemos la flag de la maquina

-----
# Aclaracion

La maquina puede tener distintas ip a lo largo del apunte por que se reinicio, modifique algo o lo hice en distintos dias