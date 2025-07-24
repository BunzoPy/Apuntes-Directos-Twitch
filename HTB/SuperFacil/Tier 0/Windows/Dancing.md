---

---
#veryeasy #windows #startingpoint #smb #nmap #ping #ls 

----
# Guided Mode

1)¿Qué significa la sigla de 3 letras SMB? 
	Server Message Block 

2)¿En qué puerto funciona SMB? 
	445

3)¿Cuál es el nombre del servicio para el puerto 445 que apareció en nuestro escaneo Nmap? 
	microsoft-ds

4)¿Cuál es el 'flag' o 'switch' que podemos utilizar con la utilidad smbclient para 'listar' los recursos compartidos disponibles en Dancing? 
	-L

5)¿Cuántas acciones hay en Dancing? 
	4

6)¿Cuál es el nombre del recurso compartido al que podemos acceder al final sin contraseña? 
	WorkShares

7)¿Cuál es el comando que podemos utilizar dentro del intérprete de comandos SMB para descargar los archivos que encontremos? 
	get


---------
## [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
![[Dancing1.png]]

![[Dancing2.png]]

![[Dancing3.png]]

![[Dancing4.png]]
*TTL:* Maquina Windows
*Puertos:*
	`445`[[smb]]
	
----------
# Intrusion con [[smb]]

```shell
smbclient -L 10.129.96.14 -p 445 -N
```

![[Dancing5.png]]

Nos conectamos a la carpeta WorkShares
```shell
smbclient //10.129.96.14/WorkShares -N
```
En la carpeta de *James.P* esta la flag
![[Dancing6.png]]
CAMBIAR ULTIMA IMAGEN DE DANCING