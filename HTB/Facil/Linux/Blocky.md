---
title: Writeup blocky - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - blocky
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina blocky en Hack The Box.
keywords:
  - writeup blocky
  - hack the box blocky
  - resolución máquina blocky
  - blocky hack the box
  - htb blocky
---
-------------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Blocky)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=qCkLwPhQKpk)
- 🧠 **Explicación resumida**: 

----------

#easy #linux #ping #nmap  #ssh #/etc/hosts #unzip #gobuster #sudo-l #tratamientotty #whatweb #wappalyzer #nvim #strings 

-----------
# Guided Mode

1)¿Cómo se llama el software FTP que se ejecuta en Blocky?
	ProFTPD

2)¿Qué nombre de usuario se asigna al enumerar el sitio web?
	notch

3)¿Qué ruta relativa en el servidor web ofrece dos archivos JAR para descargar?
	/plugins

4)¿Qué contraseña hay en el archivo BlockCore.jar?
	8YsqfCTnvxAUeduzjNSXe22

*La anterior pregunta fue la flag de user.txt*
6)¿Es posible ejecutar sudo -i y obtener un shell como root?
	yes

--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.37
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.10.37 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,22,80,25565 10.10.10.37 -oN target
```

![[Blocky1.png]]![[Blocky2.png]]
*TTL:* Maquina Linux
*Puertos:*
	`21` [[ftp]]
	`22` [[ssh]]
	`80` HTTP
	`25565` Minecraft

------

# Agregar dominio a /etc/hosts

Cuando intentamos entrar a la pagina http://10.10.10.37 nos redirige a blocky.htb

![[Blocky3.png]]

Asi que vamos a ejecutar con [[nvim]] y [[sudo]] el comando `sudo nvim /etc/hosts` para modificar el archivo abriendolo como root y agregamos lo siguiente:

```
10.10.10.37 blocky.htb
```

![[Blocky4.png]]

Ahora si nos carga la pagina

![[Blocky5.png]]

--------
# [[Whatweb-wappalyzer]]

```
whatweb http://blocky.htb/
```

![[Blocky7.png]]

![[Blocky6.png]]
Creo que lo relevante es que es un worpress version 4.8

-----
# [[Gobuster]]

gobuster dir -u http://blocky.htb -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 64

![[Blocky8.png]]

El directorio que nos llama la atencion es http://blocky.htb/plugins/

![[Blocky15.png]]
Entramos y nos descargamos estos dos archivos para usarlos despues

--------
# Enumeracion de usuario y contraseña para logearnos por [[ssh]]

Enumerando la pagina, vemos que en el post http://blocky.htb/index.php/2017/07/02/welcome-to-blockycraft/ existe el usuario *notch*

![[Blocky12.png]]

Previamente cuando estabamos enumerando con [[Gobuster]] habiamos visto la existencia del directorio */plugins* que tenia dos archivos, los decargamos, y descomprimimos el *BlockyCore.jar* con [[unzip]] usando el comando `unzip BlockyCore.jar`

![[Blocky14.png]]
Ahora con [[strings]] vamos a ver el archivo *BlockyCore.class* usando el comando `strings BlockyCore.class` y vemos que nos da una posible contraseña *8YsqfCTnvxAUeduzjNSXe22*
![[Blocky13.png]]

Ahora nos vemos a conectar por [[ssh]] usando las credenciales notch:8YsqfCTnvxAUeduzjNSXe22 con el comando `ssh notch@10.10.10.37 -p 21`

Ya estamos adentro

![[Blocky16.png]]

---------
# [[Tratamiento de la TTY]]

-------
# Escalada de privilegios con [[Abuso de Sudo]]

Usamos el comando `sudo -l`

![[Blocky17.png]]
Esto significa que podemos ejecutar cualquier comando sin contrtaseña

Asi que vamos a ejecutar una /bin/bash con permisos de sudo con `sudo /bin/bash`
![[Blocky18.png]]
Y listo ya somos root y podemos catear las flags

![[Blocky19.png]]

---------



