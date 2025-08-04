---
title: "Writeup fawn - Hack The Box - Resolución y Análisis"
published: true
tags:
  - hackthebox
  - writeup
  - fawn
  - ciberseguridad
  - pentesting
description: "Writeup y resolución de la máquina fawn en Hack The Box."
keywords:
  - writeup fawn
  - hack the box fawn
  - resolución máquina fawn
  - fawn hack the box
  - htb fawn
---
-----
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+0/Linux/Fawn)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=wnDGzJwlfuM)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=6TA2cGrCA4A)

---

#linux #veryeasy #startingpoint #ftp #ls #nmap #ping #cat

----
# Guided Mode

1)¿Qué significa la sigla de 3 letras FTP?
	File Transfer Protocol

2) ¿En qué puerto escucha habitualmente el servicio FTP?
	21

3)FTP envía datos en claro, sin cifrar. ¿Qué acrónimo se utiliza para un protocolo posterior diseñado para proporcionar una funcionalidad similar a FTP pero de forma segura, como una extensión del protocolo SSH?
	SFTP

4)¿Cuál es el comando que podemos utilizar para enviar una solicitud de eco ICMP para probar nuestra conexión con el objetivo?
	ping

5)Según tus análisis, ¿qué versión de FTP se está ejecutando en el objetivo?
	vsFTPd 3.0.3
	
6)Según tus escaneos, ¿qué tipo de sistema operativo se está ejecutando en el objetivo?
	Unix

7)¿Cuál es el comando que debemos ejecutar para mostrar el menú de ayuda del cliente “ftp”?
	FTP -?

8)¿Cuál es el nombre de usuario que se utiliza a través de FTP cuando se desea iniciar sesión sin tener una cuenta?
	anonymous

9)¿Cuál es el código de respuesta que obtenemos para el mensaje FTP «Inicio de sesión correcto»?
	230

10)Hay un par de comandos que podemos utilizar para listar los archivos y directorios disponibles en el servidor FTP. Uno es dir. Cuál es el otro que es una forma común de listar archivos en un sistema Linux.
	[[ls]]

11)¿Cuál es el comando utilizado para descargar el archivo que encontramos en el servidor FTP?
	get

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
![[Fawn1.png]]
![[Fawn2.png]]
![[Fawn3.png]]
*TTL:* Maquina Linux
*Puertos:*
	`21`[[ftp]]
*Datos importantes:*
Nos da como resultado una vulnerabilidad de [[ftp]] que nos podemos conectar como el usuario anonymous sin proporcionar contraseña

-----------
# Intrusion por [[ftp]]

Entramos con el comando `ftp 10.129.250.6`, nos logeamos con el usuarios anonymous sin poner contraseña, con [[ls]] vimos los archivos que habia en el directorio, con get descargamos el archivo flag.txt y ya teniamos el archivo en nuestra maquina para posteriormente catearlo con [[cat]] usando el comando `cat flag.txt`

![[Fawn4.png]]

-------
# Notas

A SFTP, se le agrega la s de secure