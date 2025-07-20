Aca vemos [[Magento]]

1)¿Qué versión del servicio se encuentra ejecutándose en el puerto 80?
	nginx 1.14.2

2)¿Cuál es el código de estado HTTP de 3 dígitos que se devuelve al visitar http://{machine IP}/?
	302
	Usamos el comando `curl -v http://10.129.1.27`        [[curl]]
	![[Ignition5.png]]

3)¿Cuál es el nombre de host virtual por el que se espera acceder a la página web?
	ignition.htb

4)¿Cuál es la ruta completa al archivo de un ordenador Linux que contiene una lista local de pares de nombres de dominio y direcciones IP?
	/etc/hosts
	
5)Utilizar una herramienta de fuerza bruta de directorios en el servidor web. ¿Cuál es la URL completa de la página de inicio de sesión de Magento?
	http://ignition.htb/admin

6)Busca los requisitos de contraseña para Magento y también intenta buscar las contraseñas más comunes de 2023. ¿Qué contraseña da acceso a la cuenta de administrador?
	qwerty123
	https://cybernews.com/best-password-managers/most-common-passwords/
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.1.27
nmap -p- --open -sS -vvv --min-rate 5000 -n -Pn 10.129.1.27 -oG allPorts
nmap -sCV -p80 10.129.1.27 -oN target
```

![[Ignition1.png]]

![[Ignition2.png]]
Por el ttl sabemos que es una maquina linux y que el puerto 80 esta abierto corriendo un ngix

-------
# Ingreso a la pagina web y /etc/hosts

Al intentar entrar nos envia a ignition.htb asi que lo vamos a añadir al /etc/hosts

```
10.129.1.27 ignition.htb
```

![[Ignition3.png]]

------

# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

```
whatweb http://ignition.htb
```

![[Ignition4.png]]

# Enumeracion con [[Gobuster]]

```shell
gobuster dir -u http://ignition.htb -w /usr/share/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt --add-slash -t 20
```

![[Ignition6.png]]
El resultado que nos interesa es el de /admin que nos da el link

-----
# Intrusion con [[Magento]] panel de logeo 

`http://ignition.htb/admin`
Esto nos lleva a un panel de autentificacion

![[Ignition7.png]]

https://cybernews.com/best-password-managers/most-common-passwords/
Esto lo encontramos en la pregunta numero 6 de la maquina
La contraseña es: qwerty123
Entramos y ya podemos visualizar la flag
![[Ignition8.png]]



# Creditos
Writeup oficial HackTheBox
