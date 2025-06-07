**`xfreerdp`** es una herramienta de línea de comandos para conectarse a escritorios remotos usando el protocolo **RDP (Remote Desktop Protocol)**, el cual es usado comúnmente en sistemas Windows.
Normalmente se usa en el puerto 3389
#### Instalacion
apt install freerdp2-x11 

--------
# Vulnerabilidad usuario administrator
Al intentar conectarnos con el usuario administrator sin proporcionar ninguna contraseña podemos ganar acceso a la maquina
Para especificar el usuario con el que nos queremos conectar se pone el *parametro* `/u:Usuario`

---------
# Como conectarse a una maquina
```shell
xfreerdp /v:Ip:Puerto /u:Usuario
Ejemplo: xfreerdp /v:10.129.1.13:3389 /u:administrator
```
*Parametros:*
`/v:Ip:Puerto` Es para poner la ip y el puerto para conectarnos
`/u:Usuario` Se coloca el usuario con el cual nos queremos conectar

Poniendo el comando en consola, se nos va a abrir una interfaz grafica con el escritorio de la maquina victima
![[Explosion5.png]]

