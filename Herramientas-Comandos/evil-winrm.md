Evil-Winrm es una herramienta muy útil para conectarse a máquinas Windows a través del servicio **WinRM (Windows Remote Management)**
**WinRM (Windows Remote Management)** es un **servicio de Windows que permite la administración remota de máquinas** usando el protocolo **WS-Management**, basado en SOAP (XML sobre HTTP).

------
## Como establecer conexion con maquina victima
```shell
evil-winrm -i Ip -u Usuario -p Contraseña
Ejemplo: evil-winrm -i 10.129.95.234 -u administrator -p badminton
```
*Parametros:*
`-i` Es para la ip
`-u` Va el usuario
`-p` Se pone la contraseña


![[Responder 8.png]]



