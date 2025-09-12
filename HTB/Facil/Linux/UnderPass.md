---
title: Writeup underpass - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - underpass
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina underpass en Hack The Box.
keywords:
  - writeup underpass
  - hack the box underpass
  - resolución máquina underpass
  - underpass hack the box
  - htb underpass
---
----------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/UnderPass)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=D0ssizcknNo)
- 🧠 **Explicación resumida**: 

---

#easy #liunx #nmap #ping #ssh #abusobinariosmosh-server #sudo-l #john #hasid #tratamientotty #whatweb #wappalyzer #snmp #snmpwalk #gobuster 

------
# Guided Mode

1)¿Cuántos puertos TCP abiertos escuchan en el paso subterráneo?
	2

2)¿Qué puerto UDP en los 100 puertos NMAP Top 100 está abierto en el paso subterráneo?
	161

3)¿Qué dirección de correo electrónico hace la lista de servidores subterráneos como correo electrónico de contacto?
	steve@underpass.htb

4)¿Cuál es el nombre de la aplicación alojada en el servidor web de paso subterráneo?
	daloRADIUS

5)¿Cuál es la ruta relativa en el servidor web para la página de inicio de sesión del operador para Daloradius?
	/daloradius/app/operators/login.php

6)¿Cuál es la contraseña para el usuario del administrador en la aplicación Daloradius?
	radius

7)¿Cuál es la contraseña de texto BORRAR del usuario de SVCMOSH en un paso subterráneo?
	underwaterfriends

*La pregunta anterior es la flag de user.txt*
9)¿Cuál es la ruta completa del comando que el usuario de SVCMOSH puede ejecutar como cualquier usuario?
	/usr/bin/mosh-server

--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.48
nmap -p- --open --min-rate 5000 -sS -n -Pn -vvv 10.10.11.48 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.10.11.48 -oN targetTCP
nmap -sU --top-ports 200 -n -Pn 10.10.11.48 -oN targetUDP
nmap -sU -sCV -p161 10.10.11.48 -oN udp-scan
```

![[UnderPass1.png]]

![[UnderPass2.png]]

![[UnderPass4.png]]

![[UnderPass19.png]]

*TTL:* Maquina Linux
*Puertos TCP:*
	`22` [[ssh]]
	`80` HTTP
*Puertos UDP:*
	`161` [[snmpget]]

*Datos relevantes:*
	Nos informa que esta corriendo la version 1 y la version 3. ``SNMPv1 server; net-snmp SNMPv3 server (public)``


--------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.11.48/
```

![[UnderPass6.png]]

![[UnderPass5.png]]

No nos dio ninguna informacion relevante

----------
# Dumpeo de datos con [[snmpwalk]]

```shell
snmpwalk -v1 -c public 10.10.11.48
```

![[UnderPass18.png]]
Nos dice que esta corriendo un servidor daloradius

----
# [[Gobuster]]
#### Aclaracion: En los diccionarios que usamos siempre de Seclists no encontre la palabra daloradius. Asi que buscando en internet encontramos este directorio */daloradius*

```shell
gobuster dir -u http://10.10.11.48/daloradius -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 40
```
![[UnderPass17.png]]

Encontramos el directorio */app*
![[Underpass22.png]]
Nos da un forbidden asi que vamos a seguir enumerando este directorio

```shell
gobuster dir -u http://10.10.11.48/daloradius/app -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 40 .x php,html,txt,bak,swp
```

![[UnderPass16.png]]
Encontramos el directorio http://10.10.11.48/daloradius/app/operators y nos redirige a  http://10.10.11.48/daloradius/app/operators/login.php que es un panel de logeo de administradores, y el otro de /users es para usuarios normales
![[UnderPass15.png]]
Buscando en internet encontramos las default credentials
![[UnderPass14.png]]
administrator:radius
Las credenciales son validas y ya nos pudimos logear



## Enumeracion de la pagina web

Buscando otros usuarios registrados encontramos
![[Underpass24.png]]
svcMosh:412DD4759978ACFCC81DEAB01B382403


## Crakeamos la contraseña
La contraseña pase por [[hashid]] con el comando `hashid 412DD4759978ACFCC81DEAB01B382403` y nos sale que puede ser MD5
![[UnderPass10.png]]

Asi que con [[john]] usamos el comando `john test.txt -w=/usr/share/wordlists/rockyou.txt`
![[UnderPass11.png]]

y nos dio un error de que tnemos que especificar el formato asi que cambiamos a `john test.txt --format=Raw-MD5 -w=/usr/share/wordlists/rockyou.txt`
![[UnderPass12.png]]
Nos da como resultado la contraseña: *underwaterfriends*


### Conexion por [[ssh]] al usuario *svcMosh*
```shell
ssh svcMosh@10.10.11.48 -p 22
```

Contraseña que vamos a usar para logearnos: *underwaterfriends*

![[UnderPass9.png]]

------

# [[Tratamiento de la TTY]]

--------
# Escalada de privilegios con [[Abuso de Sudo]] del [[Binario mosh-server]]

```shell
sudo -l
```

![[UnderPass8.png]]
Encontramos que podemos ejecutar el binario `/usr/bin/mosh-server` como cualquier usuario sin proporcionar contraseña. Y nos vamos a abusar de esto ejecutandolo como root 

Viendo este [articulo](https://www.hackingdream.net/2020/03/linux-privilege-escalation-techniques.html)

![[Underpass21.png]]
Si ejecutamos la instruccion `mosh --server="sudo /usr/bin/mosh-server" localhost` 

![[Underpass23.png]]

Ganamos acceso como el usuario root y ya podemos catear las flags

![[UnderPass7.png]]

------
# Notas

Al ver el apache recién instalado en el codigo fuente dice `<!-- Modified from the Debian original for Ubuntu Last updated: 2022-03-22 See: https://launchpad.net/bugs/1966004 -->` lo que es totalmente normal en un apache sin modificaciones



Cuando estaba logeado como admin en la pagina de daloradius encontre estas credenciales que no las usamos

![[UnderPass13.png]]
steve:testing123