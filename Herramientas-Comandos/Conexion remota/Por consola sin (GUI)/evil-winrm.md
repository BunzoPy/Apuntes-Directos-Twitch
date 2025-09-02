#evil-winrm 

-----

Evil-Winrm es una herramienta muy útil para conectarse a máquinas Windows a través del servicio **WinRM (Windows Remote Management)**
**WinRM (Windows Remote Management)** es un **servicio de Windows que permite la administración remota de máquinas** usando el protocolo **WS-Management**, basado en SOAP (XML sobre HTTP).
En [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]] nos va a aparecer de esta forma `5985/tcp  open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)` . Esto pasa porque el servicio **WinRM** corre encima de **Microsoft HTTPAPI** (básicamente un mini servidor HTTP de Windows que usan varios servicios del sistema).

------
## Como establecer conexion con maquina victima
```shell
evil-winrm -i Ip -u Usuario -p Contraseña -P Puerto
Ejemplo: evil-winrm -i 10.129.95.234 -u administrator -p badminton -P 5985
```
*Parametros:*
`-i` Es para la ip
`-u` Va el usuario
`-p` Se pone la contraseña
`-P` Se coloca el puerto




