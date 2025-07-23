#linux #Veryeasy #startingpoint #telnet #nmap #ping #cat #ls

-----------
# Guided mode

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
	[[telnet]]

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
*TTL:* Maquina Linux
*Puertos*
	`23`[[telnet]]

----------
# Intrusión por [[telnet]]

Usamos el servicio de [[Apuntes Directo/Herramientas-Comandos/telnet|telnet]] para conectarnos a la maquina
```shell
telnet 10.129.167.25 23 
```
Cuando nos pide que pongamos el usuario con el que nos queremos conectar, colocamos *root*
![[Meow2.png]]
Una vez dentro como root con el comando [[ls]] listamos los archivos que hay en el directorio. Visualizamos un archivo *flag.txt* y lo cateamos con [[cat]] con el comando `cat flag.txt`

![[Meow3.png]]
Y listo ya tenemos la flag de la maquina