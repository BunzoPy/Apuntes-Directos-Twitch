Server Message Block (bloque de mensajes del servidor). Es un **protocolo de red** que permite el intercambio de archivos, impresoras, puertos serie y otros recursos entre computadoras en una red
Funciona por el puerto 445 normalmente, y aparece como microsoft-ds que esto indicaria que esta activo el SMB

### Como listar directorios
```shell
smbclient -L Ip -p Puerto -N
Ejemplo: smbclient -L 10.129.96.14 -p 445 -N
```
*Parametros:*
	`-L` para listar los recursos compartidos
	`-p` Especificar el puerto
	`-N` No solicita contraseña
![[Dancing5.png]]


### Como conectarnos
```shell
smbclient //Ip/Directorio -p Puerto -N
Ejemplo: smbclient //10.129.96.14/WorkShares -p 445 -N
```

![[Dancing7.png]]