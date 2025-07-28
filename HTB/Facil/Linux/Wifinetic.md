#easy #linux #nmap #ping #ftp #ssh #grep #cat #tratamientotty #reaver #ifconfig #hostapd #ps-faux

--------
# Guided Mode

1)¿Cuál es el nombre del archivo de copia de seguridad de OpenWRT accesible a través de FTP?
	backup-OpenWrt-2023-07-26.tar

2)¿Cuál es la contraseña WiFi para SSID OpenWRT?
	VeRyUniUqWiFIPasswrd1! 

3)¿Qué usuario reutilizó la contraseña WiFi en su cuenta local?
	netadmin

*La pregunta anterior era la flag de user.txt*
5)¿Qué software de demonio de espacio de usuario se está utilizando para crear servidores de punto de acceso y autenticación?
	hostapd

6)¿Qué interfaz se está utilizando para la supervisión?
	mon0

7)¿Cuál es la contraseña WPA de la red en la interfaz mon0?
	WhatIsRealAnDWhAtIsNot51121!

------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.247
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.10.11.247 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,22,53 10.10.11.247 -oN target
```

![[Wifinetic3.png]]

![[Wifinetic2.png]]

![[Wifinetic1.png]]
*TTL:* Maquina linux
*Puertos:*
	`21` [[ftp]]
	`22`[[ssh]]
	`53` tcpwrapped | domain
*Datos importantes:* Se puede coknectar a [[ftp]] con usuario anonymous

-----
# Conexion a [[ftp]] con usuario anonymous

Nos conectamos al servicio de [[ftp]] y con el comando `ftp 10.10.11.247 -p 21` nos conectamos poniendo de usuario *anonymous* y luego con el comando `prompt` vamos a desactivar la confirmacion inyeractiva al usar el siguiente comando `mget *` para descargar todos los archivos

![[Wifinetic4.png]]

--------
# Enumeracion de los archivos obtenidos y ver [[Posibles archivos con contraseñas]] y conexion por [[ssh]]
Tenemos todos estos arcivos que descargamos previamente, ahora vamos a descomprimir el archivo de backup con `tar xf backup-OpenWrt-2023-07-26.tar`                  | [[Comprimidores de archivos]]

![[Wifinetic6.png]]
 Entramos a la carpeta etc del archivo que descomprimimos y vamos a buscar contraseñas con [[grep]] con el comando `grep -riE "pass|pass.*|.*pass"`

![[Wifinetic5.png]]
Nos aparece la contraseña: *VeRyUniUqWiFIPasswrd1!*

Ahora nos ponemos a numerar usuarios y dentro del archivo passwd que lo abrimos con [[cat]] usando el comando `cat passwd`. Vemos el usuario *netadmin*
Ya conseguimos un usuario y contraseña, como vimos en el escaneo de nmap, estaba el servicio [[ssh]] corriendo asi que vamos a probar las credenciales: netadmin:VeRyUniUqWiFIPasswrd1! 

```shell
ssh netadmin@10.10.11.247 -p 22
```

![[Wifinetic7.png]]
Ya estamos adentro de la maquina

---
# [[Tratamiento de la TTY]]

-------
# Escalada de privilegios | [[Con reaver dumpear contraseña wifi]]

Vamos a listar que procesos estan corriendo con [[ps faux y ps -eo]] 
```shell
ps -faux
```
![[Wifinetic9.png]]
Vemos que esta corriendo el servicio [[hostapd]], asi que vamos a ver por que interfaz de red puede estar corriendo el servicio. Usamos [[ifconfig]] poniendo en consola `ifconfig`. Y vemos que nos parece sospechosa la red *mon0*

![[Wifinetic10.png]]
Con la herramienta [[reaver]] vamos a dumpear la contraseña

```
reaver -i mon0 -b 02-00-00-00-02-00-30-3A-00-00-00-00-00-00-00-00 -vv
```

![[Wifinetic11.png]]
Obtenemos la contraseña: *WhatIsRealAnDWhAtIsNot51121!*
Vemos si reutiliza la contraseña para el usuario *root*. Y pudimos entrar con la contraseña que sacamos previamente. Y ya podemos catear las flags
![[Wifinetic8.png]]