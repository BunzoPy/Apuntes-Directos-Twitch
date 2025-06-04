1)¿Qué significan las siglas VM?
	Virtual Machine o en español maquina virtual

2)¿Qué herramienta utilizamos para interactuar con el sistema operativo con el fin de emitir comandos a través de la línea de comandos, como el de iniciar nuestra conexión VPN? También se conoce como consola o shell.
	Terminal

3) ¿Qué servicio utilizamos para formar nuestra conexión VPN en los laboratorios HTB?
	openpvn

4)¿Qué herramienta utilizamos para probar nuestra conexión al objetivo con una solicitud de eco ICMP?
	ping

5)¿Cómo se llama la herramienta más común para encontrar puertos abiertos en un objetivo?
	nmap

6)¿Qué servicio identificamos en el puerto 23/tcp durante nuestras exploraciones?
	telnet
	**Telnet** es un **protocolo de red** que permite a un usuario conectarse a otro dispositivo a través de una red (como Internet o una red local) usando una **línea de comandos remota**.
	Hoy en día ha sido reemplazado en la mayoría de los casos por **SSH**, que es mucho más seguro.

7)¿Qué nombre de usuario es capaz de iniciar sesión en el objetivo a través de telnet con una contraseña en blanco?
	root



---
# [[Reconocimiento de OS(Sistema operativo) y puertos abiertos con NMAP]]
```shell
ping -c 1 10.129.167.25
nmap -p- --open -n -Pn -vvv -sS --min-rate 5000 10.129.167.25
nmap -sCV -p23 10.129.167.25   
```
![[Meow1.png]]


![[Imagenes Directo/Meow2.png]]
Nos muestra que el puerto 23 esta abierto y corriendo el servicio telnet

Usamos el servicio de [[Apuntes Directo/Herramientas-Comandos/telnet|telnet]] para conectarnos a la maquina y ver la flag
```shell
telnet 10.129.167.25 23 
```
![[Imagenes Directo/Meow3.png]]